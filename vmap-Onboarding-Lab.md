The goal of this lab is to give first hand experience with writing vmap rules (aka batching rules) and adding them to PyTorch. An alternative to this lab is taking on real tasks to add batching rules to PyTorch.

The deliverable is a stack of PRs (that won't be merged into master) containing all of the code for the different sections below. The task will be considered finished when the PR is accepted by the reviewers and you can just close it at that time.

## Considered Function

For this lab, we'll be writing a batching rule for a new operator, `simple_mul` that is similar to `torch.mul`, but has more restrictions on its input types. Concretely, `simple_mul` looks like the following:
```py
def simple_mul(x: Tensor, y: Tensor):
    return torch.mul(x, y)
```

For the sake of learning, please do not read the existing batching rule for torch.mul. When the lab is complete, the following test cases should work:

```py
import torch
from functorch import vmap

# The dimension being vmapped over
B = 2
B1 = 3

for op in [torch.mul, torch.simple_mul]:
    # A) Simple case
    x = torch.randn(B)
    y = torch.randn(B)
    vmap(op)(x, y)

    # B) vmap over some Tensors
    x = torch.randn(3)
    y = torch.randn(B)
    vmap(op, in_dims=(None, 0))(x, y)

    # C) More complicated case
    x = torch.randn(4, 3)
    y = torch.randn(3, B)
    vmap(op, in_dims=(None, 1))(x, y)

    # D) Nested vmap
    x = torch.randn(B)
    y = torch.randn(B1)
    vmap(vmap(op, (0, None)), (None, 0))(x, y)
```

## I) Determine how to rewrite each of the above expressions without using `vmap`.

For example, in case A, `vmap(torch.mul)(x, y)` can be rewritten as `torch.mul(x, y)`.

## II) Write the batching rule for `simple_mul` in Python

Write a function in Python with the following signature:
```py
def mul_batched(in_dims: Tuple[Optional[int], Optional[int]], x: Tensor, y: Tensor) -> Tensor:
    pass
```
`mul_batched(in_dims, x, y)` should return the same thing as `vmap(simple_mul, in_dims)(x, y)`.
Do not use vmap in the definition of `mul_batched`.

Test it out with the above cases.

## III) Write native composite function and OpInfo

Write a brand new CompositeImplicitAutograd function in [native_functions.yaml](https://github.com/pytorch/pytorch/blob/master/aten/src/ATen/native/native_functions.yaml) called `simple_mul`.

Add [an OpInfo entry](https://github.com/pytorch/pytorch/blob/master/torch/testing/_internal/common_methods_invocations.py)
for it to test it. Don't use `BinaryUfuncInfo` (that's beyond the scope of this lab) as the constructor; just use OpInfo
and provide the above test cases (and more, if you wish) as the sample inputs.

When this step is done, you'll have a `torch.simple_mul` operator that is callable from Python as well as an OpInfo test for it.
Run 
```
pytest test/functorch/test_vmap.py -v -k "simple_mul"
```
to verify that the OpInfo is correctly hooked up to our vmap testing.

## IV) Write custom batching rule.

Mark the operator as CompositeExplicitAutograd and add vmap support for `torch.simple_mul` in [BatchRulesBinaryOps.cpp](https://github.com/pytorch/pytorch/blob/2a37ba8e81604fda5ba78fe5ee8c8662ce0c25f3/aten/src/ATen/functorch/BatchRulesBinaryOps.cpp#L322). The goal is to get the following
tests to pass:
- TestVmapOperators.test_vmap_exhaustive_simple_mul*
- TestVmapOperators.test_op_has_batch_rule_simple_mul*
Please do this step by adding the following C++ function, which should be a transcription of your Python `mul_batched` into C++.
```cpp
std::tuple<Tensor,optional<int64_t>> simple_mul_batch_rule(
    const Tensor& x, optional<int64_t> x_bdim,
    const Tensor& y, optional<int64_t> y_bdim) {
  // your code here.
}
```
Avoid using the BINARY_POINTWISE macro (that would solve the problem trivially).


