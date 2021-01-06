## Numerics

### Does PyTorch distinguish between different types of NaN like C++ does?

No. PyTorch treats NaNs like Python does: there is only one floating point NaN and a complex number is NaN if either its real part is NaN, its imaginary part is NaN, or both parts are NaN. 

### Why doesn't PyTorch let me compare complex numbers like NumPy?

Complex numbers are not part of any totally ordered field, so comparisons between them are ambiguous. PyTorch follows Python3 in disallowing these comparisons. NumPy intends to update its behavior to match Python3 in the future, too. See the NumPy issue [here](https://github.com/numpy/numpy/issues/15981). 

## PRs

### My PR hasn't been reviewed in awhile, what should I do? 

You can and should ping reviewers by ccing them in a comment if they haven't responded to a review request in 2 full working days. For example, if you request a review on Friday, you can ping your reviewers on Wednesday of the next week. 

### If I'm editing the documentation, how do I see the formatted doc?

See [here](https://github.com/pytorch/pytorch/blob/master/CONTRIBUTING.md#building-documentation) for instructions on building the docs locally.

If you've submitted a PR, click the "Details" link next to the pytorch_python_doc_build, then go to the "Artifacts" tab. It has links to the documentation built for your PR. 

### My PR caused a build to fail, but it's not usually run in the Github CI. How do I test my PR against EVERY build?

Push your changes to a pytorch/pytorch branch named ci-all/<your name here>. That will trigger all the test builds, not just the ones that typically run.

### My PR was rejected for being "too small" &mdash; what gives!?

Each PR takes costly machine and developer time. "Small" PRs that may correct a spelling error or making a tiny grammar change are simply not worth the cost.

### I have a new function or feature I'd like to add to PyTorch, should it be in "PyTorch Core" or a library like torchvision?

First, thanks for wanting to add new functionality to PyTorch!

Typically a new function or feature requires an issue for discussion, followed by a consensus that the function/feature will be accepted into PyTorch Core. Good candidates for acceptance have one or more of the following properties:

- further a current PyTorch UX or functionality goal
- are derived from frequently used and established research that's been vetted for 2+ years
- add necessary functionality to support research that does not conflict with PyTorch's UX

Many function/feature proposals are interesting and derived from compelling research but lack vetting from the community. These ideas are best implemented in PyTorch-compatible libraries to incubate. Numerous functions or features related to a specific research area are also probably best implemented in a library. There is a reason, for example, that torchvision is not part of PyTorch Core. 

## UX

### How does out= work in PyTorch?

When a user passes one or more tensors to out= the contract is as follows:

- if an out tensor has no elements it may be resized
- passing out= tensors is numerically equivalent to performing the operation and "safe" copying its results to the (possibly resized if empty) out tensors

A "safe" copy is different from PyTorch's regular copy because it requires the to tensor's device be the same as from tensor's, and, for computations that have a "computation type" (like those participating in type promotion) the copy cannot be to a lower "type kind." PyTorch has four type kinds: boolean, integer, float, and complex, in that order. So, for example, an operation like add will throw a runtime error if given float inputs but an integer out= tensor.

Note that while the numerics of out= are that the operation is performed and then its results are "safe" copied, behind the scenes operations may reuse the storage of out= tensors and fuse the copy for efficiency. Many operations, like add, perform these optimizations. 

### How do in place operations work in PyTorch?

In place operations in PyTorch operate directly on their input tensor's memory. These operations typically have an underscore at the end of their name to specify they're inplace. For example, torch.add(a, b) produces a tensor c with its own storage, but a.add_(b) modifies a's data. From a UX standpoint, these operations are equivalent to specifying the modified tensor as an "out tensor." That is, torch.add(a, b, out=a) is equivalent to a.add_(b). 

One caveat with this thinking is that gradients for operations using the out kwarg are not supported, but gradients are often supported for operations performed in place.

### Reshaping operations and returning self, a view, or a tensor with its own storage

Reshaping operations in PyTorch manipulate a tensor's shape without modifying its elements. Examples of these operations are `contiguous()`, `reshape()`, and `flatten()`. These operations have different options for what to return:

- if the operation is a nullop, then the input can be returned, a view of the input can be returned, or a copy of the input can be returned
- if the operation produces a shape that can be a view of the input, a view of the input or a copy of the input can be returned
- if the operation produces a shape that cannot be a view of the input, a copy of the input must be returned

PyTorch aggressively prefers returning self when possible, then a view, then making a copy of the input data. This can be confusing, since operations like `reshape()` can sometimes produce a view and sometimes produce a tensor that doesn't share storage with its input, and these results have different write semantics. PyTorch has decided, however, to bias towards performance and memory use in these cases. Programs performing inplace operations must be mindful of this behavior.

### When should I TORCH_WARN vs. TORCH_WARN_ONCE?

PyTorch offers both [TORCH_WARN](https://github.com/pytorch/pytorch/blob/4f538a2ba48afeb2a2a1f3b6e01b1ec461d4a5ed/c10/util/Exception.h#L391) and [TORCH_WARN_ONCE](https://github.com/pytorch/pytorch/blob/4f538a2ba48afeb2a2a1f3b6e01b1ec461d4a5ed/c10/util/Exception.h#L402). The latter, as the name suggests, will cause a warning to be triggered one time per process, while TORCH_WARN will throw a warning every time.

Generally you should use TORCH_WARN_ONCE. Warning the user every time a behavior occurs is often onerous, especially if the warning occurs in a network's training loop. Only use TORCH_WARN in the following situations:

- a user opted-in to the warnings, like by enabling anomaly mode
- a non-critical operation failed

For all other situations, like deprecation warnings and behavior that may result in crashes, use TORCH_WARN_ONCE.
