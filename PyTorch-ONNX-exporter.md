Documentation for developing the PyTorch-ONNX exporter (`torch.onnx`).

# Table of Contents

<!-- TOC generated with https://github.com/ekalinin/github-markdown-toc -->

* [Development process](#development-process)
   * [Environment setup](#environment-setup)
      * [Fork PyTorch](#fork-pytorch)
      * [Build PyTorch](#build-pytorch)
      * [Install additional dependencies](#install-additional-dependencies)
      * [Sanity check](#sanity-check)
   * [Pull requests](#pull-requests)
   * [Tests](#tests)
      * [Adding tests](#adding-tests)
* [Links](#links)
   * [Relevant parts of PyTorch repo](#relevant-parts-of-pytorch-repo)

# Development process

## Environment setup

We highly recommend using Linux. Other platforms are not tested in PyTorch CI and
are generally not used by the torch.onnx developers.

### Fork PyTorch

[Fork](https://docs.github.com/en/get-started/quickstart/fork-a-repo) github.com/pytorch/pytorch and clone your fork to your workstation.

### Build PyTorch

CUDA is not required for most development tasks. If you use CUDA, building PyTorch will probably be slower.

See the instructions in PyTorch's [README](https://github.com/pytorch/pytorch/blob/master/README.md#from-source).

Optional but highly recommended: [PyTorch C++ development tips](https://github.com/pytorch/pytorch/blob/master/CONTRIBUTING.md#c-development-tips).

### Install additional dependencies

Install the dependencies required to run CI checks locally.

```sh
# flake8 version restriction due to https://github.com/pytorch/pytorch/issues/69500
conda install -c conda-forge expecttest pytest mypy 'flake8<4'
```

#### ONNX Runtime

```sh
conda install -c conda-forge onnxruntime
```

If you need a newer or different version of ONNX Runtime than what is available via conda, you can instead install
it [from source](https://onnxruntime.ai/docs/build/inferencing.html) or
[pip](https://onnxruntime.ai/docs/install/).

#### TorchVision

```sh
# cpuonly not needed if you're using CUDA
conda install -c pytorch-nightly torchvision cpuonly
```

This installs PyTorch as a dependency, but it should be ignored in favor of the version you build
with `setup.py` if you run `python` from the root of the pytorch repo.

### Sanity check

You should be able to run these commands successfully:

```sh
git fetch upstream onnx_ms_1
git checkout upstream/onnx_ms_1
python setup.py develop
python -m pytest test/onnx/test_pytorch_onnx_onnxruntime.py::TestONNXRuntime::test_arithmetic_prim_long
```

And this should fail:

```sh
echo "assert False >> torch/onnx/utils.py"
python -m pytest test/onnx/test_pytorch_onnx_onnxruntime.py::TestONNXRuntime::test_arithmetic_prim_long
git restore torch/onnx/utils.py
```

If the second command succeeds, then you're somehow using

## Pull requests

Most PRs should be opened against the branch [onnx_ms_1](https://github.com/pytorch/pytorch/tree/onnx_ms_1) instead of master.
This branch will be periodically merged into master. This lets the torch.onnx developers avoid merge conflicts with each other
without waiting for our PRs to be merged into master (which can take a long time).

Start your work by creating a local branch based on `onnx_ms_1`:

```sh
git checkout upstream/onnx_ms_1
git switch -c {your_branch_name}
```

If you make significant changes to non-ONNX related code, then open the PR against master instead of `onnx_ms_1`.

See [GitHub pull request workflow](https://docs.github.com/en/get-started/quickstart/github-flow).

Adhere to [Google's Code Review Developer Guide](https://google.github.io/eng-practices/review/).

Feel free to ignore a failing GitHub check that:

* Doesn't have "onnx" in the name
* Seems unrelated to your change

## Tests

Running all the tests locally takes a very long time, so generally you should run a few tests locally and rely on
GitHub CI checks for comprehensive testing.
We highly recommend using [pytest to run tests selectively](https://docs.pytest.org/en/latest/how-to/usage.html).
Note that you should use `python -m pytest` rather than calling `pytest` directly to make sure it uses your locally
built version of PyTorch.

Most relevant tests are in [test/onnx/](https://github.com/pytorch/pytorch/tree/onnx_ms_1/test/onnx).

The most used test file is [test_pytorch_onnx_onnxruntime.py](https://github.com/pytorch/pytorch/blob/onnx_ms_1/test/onnx/test_pytorch_onnx_onnxruntime.py). The tests in this file generally:

* Define a subclass of `torch.nn.Module`.
* Define some inputs.
* Call `self.run_test()` with the instantiated module and inputs.

`run_test()` converts the module to ONNX and compares the output between PyTorch and ONNX Runtime.

Tests added to `TestONNXRuntime` are automatically defined for all supported opset versions.
The class name with no suffix runs tests against opset version 9.

For example:

```sh
# run test for opset 11
python -m pytest test/onnx/test_pytorch_onnx_onnxruntime.py::TestONNXRuntime_opset11::test_arithmetic_prim_bool
# run test for opset 9
python -m pytest test/onnx/test_pytorch_onnx_onnxruntime.py::TestONNXRuntime::test_arithmetic_prim_bool
```
An example of adding unit tests for a new symbolic function: [Add binary_cross_entropy_with_logits op](https://github.com/pytorch/pytorch/pull/49675)

# Links

* [User-facing docs](https://pytorch.org/docs/master/onnx.html).

## Relevant parts of PyTorch repo

* User-facing doc: [docs/source/onnx.rst](https://github.com/pytorch/pytorch/blob/onnx_ms_1/docs/source/onnx.rst)
* Python tests: [test/onnx/](https://github.com/pytorch/pytorch/tree/onnx_ms_1/test/onnx)
* More Python tests: [test/jit/test_onnx_export.py](https://github.com/pytorch/pytorch/tree/onnx_ms_1/test/jit/test_onnx_export.py)
* Python code: [torch/onnx/](https://github.com/pytorch/pytorch/tree/onnx_ms_1/torch/onnx)
* C++ code: [torch/csrc/jit/passes/onnx/](https://github.com/pytorch/pytorch/tree/onnx_ms_1/torch/csrc/jit/passes/onnx)
