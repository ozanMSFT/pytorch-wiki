# Adding Op for MPS Backend

This page covers references about adding a new Op for the MPS backend.

There are a few options to add support. We usually want to follow these in order:
- If MPS has an existing op for the give PyTorch function, just add a new native function that link the two. An example PR that does thiscan be found [here](https://github.com/pytorch/pytorch/pull/78408).
- Otherwise, for ops that require high performance implementation, we can write custom Metal kernel. There are no example for this yet, feel free to open an issue to discuss this if you're interested.
- As a last resort for ops that are not perf critical, we can make them use the CPU by default. This can be done similarly to first bullet point but the native implementation is replaced by a new entry in [here](https://github.com/pytorch/pytorch/blob/3524428fad4e274fd9145595609effeb775cb51c/aten/src/ATen/mps/MPSFallback.mm#L38).

# View Ops in MPS using Gather-Scatter approach

### Introduction: 

PyTorch allows a tensor to be a View of an existing tensor. The View tensors are sharing the same underling storage data as the parent tensor, so they are avoiding an explicit data copy at creation.  

The newly created View might have strides - the stride represents the necessary jump to go from one element to the next one in the specified dimension dim. Currently to support strides with MPS kernels (not supported), we are performing a Gather-Scatter approach.

Some of the most common examples include:

* Slicing:

`
x = torch.tensor([1, 2, 3, 4], device="mps")
res = x[:2]
`

* Expand
* Narrow
* Permute
* Squeeze

All the view operations will call into as_strided in order to create the View tensor:

`
Tensor as_strided_tensorimpl_mps(const Tensor& self, IntArrayRef size, IntArrayRef stride, optional<int64_t> storage_offset_)
{
  auto storage_offset = storage_offset_.value_or(self.storage_offset());
  auto result = detail::make_tensor<TensorImpl>(c10::TensorImpl::VIEW, Storage(self.storage()), self.key_set(), self.dtype());
  setStrided(result, size, stride, storage_offset);

  // 0 sizes won't result in any change in the shape of the Tensor so we can skip it.
  if (size.size() > 0)
    mps::createViewGraph(self, size, stride, storage_offset, /*needsScatter*/ false);

  return result;
}
`

At this step, when the View tensor is initialized, we are creating a generic graph which we will later use to materialize the data. The graph that we are creating will Gather the data that the View tensor requested from the original Tensor. For the Gather graph, we are performing the following algorithm.

Inside mps::createViewGraph we are creating a generic graph based on the original Tensor, the new shape of the View tensor and the dtype of the original Tensor, then we will cache this graph so other similar View operations could reuse it:

`
string key = getStridedKey(self.scalar_type(), base_shape, size, needsScatter);
cachedGraph = static_cast<ViewCachedGraph *>(cache_->CreateCachedGraph(key, ^ MPSCachedGraph * ()
`

Note that we store the shape of the original tensor in its buffer block in MPSAllocator, so it could be retrieved at the time of gather or when we need the original shape of the tensor when we’re creating a view from it.  

Gather algorithm :

To create the Graph, we are using the following algorithm:

`
// 1. Create 1D *indicesTensor*:
//      Based on the *strides* and the *storage_offset* of the View, create a list of
//      indices that we need to gather from the original Tensor 
// 2. Reshape the *inputTensor* to 1D as well, so we can index it using the indicesTensor
// 3. Perform a Gather operation:
//     *gatherResult* = [graph gatherWithUpdatesTensor: *inputTensor1D*
//                                     indicesTensor: *indicesTensor1D* ...]; 
// 4. Reshape the gathered data (*gatherResult*) back to the original shape of the View:
//     *outputTensor* = [graph reshapeTensor: *gatherResult
*//                         withShapeTensor: shapeOfViewTensor ...];
`

Materialize memory:

The above graph is cached and later used when the memory of that View is *materialized.* The View’s data could be *materialized* during the following cases:

1. In a *copy* operation:
    1. mps→cpu (copy_from_mps_) 
    2. mps→mps (copy_kernel_mps)
    3. cpu →mps (we make this contiguous on CPU and then do a blit)
2. In any PyTorch operation using View tensors:
    1. We are materializing the data of the View during the Placeholder creation:

`
Placeholder*::*Placeholder(MPSGraphTensor* mpsGraphTensor, const Tensor& src, MPSShape *mpsShape) : _tensor(src)
{
  // ... 
  if (src.is_view() || !src.is_contiguous()) {
    _tensor = gatherViewTensor(src);
  }
  // ...
} 
`

Example of Gather algorithm:

*Slicing:*

`
x = torch.tensor([1, 2, 3, 4], device="mps")
res = x[:2]
`

For this example, the algorithm works in the following way:

1. A graph is created in  createViewGraph , with the following data:
    1. *inputTensor1D* = [1, 2, 3, 4]
    2. *indicesTensor1D* = [2, 3]
    3. *gatherResult =* [3, 4]
2. Materialize memory in copy_kernel_mps (assign to res the result from x[:2])
    1. Perform a lookup for the Gather graph (created in step *1.*)
    2. If the graph is successfully found, we will create an empty contiguous tensor in which the result of gather is saved (in this example [3, 4])
    3. Allocate an temporary Tensor to save the output of gather
    4. Blit the output of gather back to *dst* 



Scatter algorithm:

In some cases, we can not simply copy gather’s result doing a blit operation. Let’s take for example the following case:

`
x = torch.tensor([[1, 2, 3], [4, 5, 6]], device="mps")
x[:,0] = 7
`

Here we are replacing the first column with 7. In this case, when copy_kernel_mps is called, we will gather from *src* and scatter back to *dst*. The passed *dst* is a View tensor of *x* ** , ** and, based on it’s strides and storage offset, we are creating an *indicesTensor* (similar as in Gather Algorithm). Based on the indicesTensor we will do an in-place scatter operation and write back the gathered values from src into dst:

`
// 1. Create 1D *indicesTensor* based on *dst*:
//      Based on the *strides* and the *storage_offset* of the View, create a list of
//      indices that we need to scatter back to the original Tensor 
// 2. Reshape the *inputTensor* to 1D, so we can index it using the indicesTensor
//    In case of Scatter, *inputTensor* is *dst*
// 3. Create a placeholder for the *updatesTensor*
//    *updatesTensor* will contain the data from *src* (after gather) 
// 3. Perform a Scatter operation:
//     *scatterResult* = [graph scatterAlongAxis: 0
//                               withDataTensor: *inputTensor1D*
//                                updatesTensor: *updatesTensor*                                  
//                                indicesTensor: *indicesTensor1D* ...];
//                               
// 4. Reshape the scattered data (*gatherResult*) back to the original shape of the *dst* View:
//     *outputTensor* = [graph reshapeTensor: *scatterResult*
//                               withShape: baseShapeOfDst ...];
`

Example of Scatter algorithm:

*Replacing a column:*

`
x = torch.tensor([[1, 2, 3], [4, 5, 6]], device="mps")
x[:,0] = 7
`
For above example, copy_kernel_mps will be called:

`
*static* at*::*Tensor& copy_kernel_mps(at*::*Tensor& dst_, const at*::*Tensor& src_, bool non_blocking)
`

* dst will be a view of x (x[:,0])
* src is a non-contiguous tensor. We will run gather for it, and the result will be [7, 7]
* The gathered result is used as updatedTensor in Scatter. If dst is a non contiguous Tensor (in this case it is), we will scatter back the results from Gather in dst . 
    * Perform in-place Scatter operation. Based on dst’s strides and storage offset, we will end up with the following scatter call:
    *  `
        //     *scatterResult* = [graph scatterAlongAxis: 0
        //                               withDataTensor: *[1, 2, 3, 4, 5, 6]*
        //                                updatesTensor: *[7, 7]*                                  
        //                                indicesTensor: *[0, 3]* ** ...];
       `

Example of Gather Scatter mixed together: 

* Slicing with step:
`
x = torch.zeros(10, dtype=torch.float32, device="mps")
x[::2] = 1.0
`
* Replacing a column:
`
x = torch.tensor([[1, 2, 3], [4, 5, 6]], device="mps")
x[:,0] = 7
`
* In-place operations:
`
a_mps = torch.ones((2, 2),).to(torch.device("mps"))
b_mps = torch.ones((2, 2),).to(torch.device("mps"))

a_mps[:, 0] += b_mps[:, 0]
a_mps[:, 0]  = a_mps[:, 0] + b_mps[:, 0]
`
* Padding:
`
y = torch.ones(2,1, device='mps')
y_pad = torch.nn.functional.pad(y, (1, 1, 1, 1), 'constant', 0)
`
