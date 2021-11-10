# Basic development usage of exporter 

We basically need PyTorch, ONNX and ONNX Runtime. Prefer using conda whenever possible, but  we may need to mix conda and pip. 

Here is an example of setting local environment from source: 

Example platform: Linux, Visual Studio Code, Python, Anaconda 

 

## Fork pytorch/pytorch on GitHub 

On github.com, navigate to the pytorch/pytorch repository.  

In the top-right corner of the page, click Fork. 

And now you get a fork of the PyTorch repository. 

 

## Set up your development dependencies environment. 

Set up the ONNX and ONNXRUNTIME environment.
```
pip install onnx, onnxruntime 
```
 
Install torchvision for running onnx tests 
```
pip install torchvision 
```

PyTorch code Common Dependencies 
```
conda install astunparse numpy ninja pyyaml mkl mkl-include setuptools cmake cffi typing_extensions future six requests dataclasses 
```
More information of building ONNX、ONNXRuntime from official source.

[ONNX build](https://github.com/onnx/onnx#installation)

[ONNXRuntime build](https://onnxruntime.ai/docs/build/)

 
## Build the PyTorch code from source 

Get the PyTorch source code and install Pytorch (will take up the pytorch from the package).   

Get PyTorch source code from your own repository using ssh. 
```
git clone git@github.com:{your github name}/pytorch.git 
```

If CUDA needed, add LAPACK support matches your CUDA version from https://anaconda.org/pytorch/repo 
```
conda install -c pytorch magma-cuda110 
```
Set CMAKE_PREFIX_PATH with conda
```
export CMAKE_PREFIX_PATH=${CONDA_PREFIX:-"$(dirname $(which conda))/../"} 
```
Build PyTorch project, it can take tozens of minutes. Or using install, develop for more convenient debugging. 
```
debug=1 use_cuda=1 python setup.py develop 
```
You can also refer: [pytorch/blob/master/README.md](https://github.com/pytorch/pytorch/blob/master/README.md#from-source )

## Make your changes and open a PR against onnx_ms_1. 

So we need to fork our feature branch from `onnx_ms_1` branch instead of master. The PRs submited will also use this branch as target and not pytorch master branch.

This branch will be periodically merged back into pytorch master. 

Any existing PRs will also have to be rebased to this branch and resubmitted.  


Create your branch based on onnx_ms_1 branch: 
```
git checkout onnx_ms_1 

git checkout –b {your_branch_name} 
```

After you made changes and push to github, "onnx_ms_1" branch may be changed by others. 

rebase the latest onnx_ms_1 and fix the confliction

```
git fetch origin onnx_ms_1 

git rebase origin/onnx_ms_1  

git push origin {your_branch}  
```

Then you can create a PR in github UI. 

Refer link: Experience rebasing  to update onnx_ms_1 

 

## Code review. 

Code Review standards 

A good guide: [How To Do A Code Review and The CL Author's Guide](https://google.github.io/eng-practices/review/)

Exporter team number has the regular to review code and deal with github issue. 


 

## Merge code into onnx_ms_1. 

The person merging a PR needs to ensure: 

Title and descriptions are meaningful, usually copying from the PR title and description. 

Author information is included: Co-authored-by author_name <author_email>. 

For example: 

Step 1: hit `Squash and merge` button 

Graphical user interface, text, application, email

Description automatically generated 

Step 2: Edit the description. Usually, one just needs to copy from the original PR description. The auto generated description consists of all the commit messages is lengthy and not helpful. 

Graphical user interface, text, application, email

Description automatically generated 

 
Step 3: Add author information by the end of the description section, to preserve original PR author info. 
 

Step 4: Confirm squash and merge. 
 

## onnx_ms_1 merge into master. 

Exporter team will periodically merge onnx_ms_1 branch into pytorch master. 

Steps to merge onnx_ms_1 into PyTorch master 


# Add an operator and run test

During export aten graph into ONNX graph, each node in the TorchScript aten graph is visited in topological order. Upon visiting a node, the exporter C++ code tries to find a registered symbolic function for that node. Symbolic functions are named `_run_symbolic_function` implemented in Python, you can also see the calling in [/torch/csrc/jit/passes/onnx.cpp#L404](https://github.com/pytorch/pytorch/blob/a6c0edff1a60e299d3f9794407f0a1c9a269fd19/torch/csrc/jit/passes/onnx.cpp#L404). 

## Add a symbolic function 

If the operator is an ATen operator, it can be done by the following steps: 

Add the symbolic function in torch/onnx/symbolic_opset_`version`.py, for example torch/onnx/symbolic_opset14.py. 

The first parameter is always the exported ONNX graph. Parameter names must EXACTLY match the names in `pytorch/aten/src/ATen/native/native_functions.yaml`

In the symbolic function, if the operator is already standardized in ONNX, we only need to create a node to represent the ONNX operator in the graph.  

Here is the `add` example symbolic function in opset 9:  
```python
def add(g, self, other, alpha=None): 

    if sym_help._is_value(self) and sym_help._is_tensor_list(self): 

        return sym_help._onnx_opset_unsupported_detailed("Add", 9, 11, "Add between list of tensors not supported") 

    # default alpha arg is to allow no-alpha add (aten add st overload no alpha) 

    if alpha and sym_help._scalar(sym_help._maybe_get_scalar(alpha)) != 1: 

        return _unimplemented("add", "alpha != 1") 

return g.op("Add", self, other) 
```


## Unit testing 

### Adding tests 

All PyTorch ONNX test suites are in [pytorch/test/onnx/](https://github.com/pytorch/pytorch/tree/onnx_ms_1/test/onnx) folder with `test_*` file, the ending string `*` represent the meaning of this test file.  

If we want to add a new feature or operator in exporter. The most used test file is in [test/onnx/test_pytorch_onnx_onnxruntime.py](https://github.com/pytorch/pytorch/blob/onnx_ms_1/test/onnx/test_pytorch_onnx_onnxruntime.py). 

Define the model class, python function and run self.run_test to test the result between PyTorch and ONNX. The test will compare the output ONNX graph with expected. 

Decide to use python decorator to skip some opset versions. Such as where op only supports for opset < 9. So we need to add `@skipIfUnsupportedMinOpsetVersion(9) to skip version < 9.`

Here is the  simple `test_add_inplace` unit test case: 

```python
    def test_add_inplace(self): 

        class InplaceAddModel(torch.nn.Module): 

            def forward(self, x): 

                x += 12 

                return x

        x = torch.randn(4, 2, 3, requires_grad=True) 

        self.run_test(InplaceAddModel(), x) 
```
 

### Running tests 

You can narrow down what you're testing even further by specifying the name of an individual test with TESTCLASSNAME.TESTNAME. Here, TESTNAME is the name of the test you want to run, and TESTCLASSNAME is the name of the class in which it is defined. 


Going off the above example, let's say you want to run test_sum in opset version 14, which is defined as part of the TestONNXRuntime_opset14 class in test/onnx/test_pytorch_onnx_onnxruntime.py. Your command would be: 

```
python test/onnx/test_pytorch_onnx_onnxruntime.py TestONNXRuntime_opset14.test_sum 
```
 

An example of adding unit tests for a new symbolic function PR: [Add binary_cross_entropy_with_logits op](https://github.com/pytorch/pytorch/pull/49675) 