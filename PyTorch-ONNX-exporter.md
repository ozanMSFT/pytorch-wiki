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
* [Features](#features)
   * [Quantized model export](#quantized-model-export)
* [Updating default opset_version](#updating-default-opset_version)

# Development process

## Environment setup

We highly recommend using Linux. Other platforms are not tested in PyTorch CI and
are generally not used by the torch.onnx developers.

### Fork PyTorch

[Fork](https://docs.github.com/en/get-started/quickstart/fork-a-repo) github.com/pytorch/pytorch and clone your fork to your workstation.

### Build PyTorch

CUDA is not required for most development tasks. If you use CUDA, building PyTorch will probably be slower.

Install [Anaconda](https://www.anaconda.com/products/individual) and activate a new environment.

Install [direnv](https://direnv.net/) and initialize your envrc file in the root of your PyTorch git repo:

```sh
# Make the local package name built by `setup.py develop` the same
# as the one that's on conda.
echo "export TORCH_PACKAGE_NAME=pytorch" >> .envrc
# Let CMake find binaries and libs installed by conda.
echo 'export CMAKE_PREFIX_PATH=${CONDA_PREFIX:-"$(dirname $(which conda))/../"}' >> .envrc
direnv allow
```

Then see the instructions in PyTorch's [README](https://github.com/pytorch/pytorch/blob/master/README.md#from-source).

#### Optional build tips

[PyTorch C++ development tips](https://github.com/pytorch/pytorch/blob/master/CONTRIBUTING.md#c-development-tips).

[Use direnv for Anaconda environment selection](https://github.com/direnv/direnv/wiki/Python#anaconda).

Set more environment variables in your .envrc file:
```sh
# Only if you're building without CUDA.
export USE_CUDA=0
# Only if you're building with ccache.
PATH_add /usr/lib/ccache
# Needed for older compilers or conda compilers
export LDFLAGS='-lrt'
# Build with debug symbols.
export DEBUG=1
```

### Install additional dependencies

Install the dependencies required to run CI checks locally.

```sh
# flake8 version restriction due to https://github.com/pytorch/pytorch/issues/69500
conda install -c conda-forge expecttest pytest mypy=0.812 'flake8<4' hypothesis
```

#### ONNX Runtime

```sh
# install a version of protobuf compatible with ONNX submodule
conda install -c conda-forge protobuf=$(cat third_party/onnx/requirements-release.txt | grep protobuf | awk '{print $3}') flatbuffers
pip install onnxruntime
```

Onnxruntime is also available from conda-forge, but it seems to demand a version of protobuf that's newer than
what the ONNX submodule wants to use, which lead to seg-faults in my case. This may be resolved with future
versions of ONNX or ONNX Runtime. If you find `conda install -c conda-forge onnxruntime` works, please update
these instructions.

If you need a newer or different version of ONNX Runtime than what is available via conda, you can instead install
it [from source](https://onnxruntime.ai/docs/build/inferencing.html).

#### TorchVision

```sh
# cpuonly not needed if you're using CUDA
conda install -c pytorch-nightly torchvision cpuonly
conda uninstall --force pytorch
```

This first command installs PyTorch as a dependency, so we remove it with the second command
so that you can use the locally-built pytorch rather than the one from conda.
If you later run into issues with conda complaining about an inconsistent environment, you
can temporarily reinstall PyTorch via conda:

```sh
conda update -c pytorch-nightly torchvision cpuonly
```

I hope there's a better way to deal with this. If you know of one please
update these instructions and email the team!

### Sanity check

You should be able to run these commands successfully:

```sh
git fetch upstream onnx_ms_1
git checkout upstream/onnx_ms_1
python setup.py develop
python test/onnx/test_pytorch_onnx_onnxruntime.py TestONNXRuntime.test_arithmetic_prim_long
```

And this should fail:

```sh
echo "assert False" >> torch/onnx/utils.py
python test/onnx/test_pytorch_onnx_onnxruntime.py TestONNXRuntime.test_arithmetic_prim_long
git restore torch/onnx/utils.py
```

If the second command succeeds, then probably python is finding the PyTorch that was installed via `conda` or `pip`, not the one that was built from source by `python setup.py develop`.

## Pull requests

PRs should be opened directly against master. PRs can be directly merged into master as long as it satisfies the [ONNX merge rule](https://github.com/pytorch/pytorch/blob/master/.github/merge_rules.json#L3):
* Approved by one of torch.onnx developers listed in `approved_by` section.
* All modified files fall under the `patterns` section.

Pay special attention to the following GitHub checks:
* Has "onnx" in the name, which runs ONNX related tests.
* Has "Lint" in the name, which does code format checks.

Regarding other failing GitHub checks, if you are certain the failure is unrelated to your change, try rebasing with master. Often times these kind of failures are caused by branch out of sync with master.
For rare occasions, You can ignore the failing check if it is a regression in master. This can be verified by checking if master is also failing from [CI HUD for PyTorch](https://hud.pytorch.org/ci/pytorch/pytorch/master).

To merge your pull request, comment on the PR "@pytorchbot merge this".

If you make changes to non-ONNX related code, i.e. files out side of [ONNX merge rule](https://github.com/pytorch/pytorch/blob/master/.github/merge_rules.json#L4), please note the PR will require additional reviews from people outside of torch.onnx developers, and will take a longer process to merge into master. In this case, pytorchbot will not be able to merge the pull request. It will leave a comment like "Merge failed due to PR XXX does not match merge rules". Please label the pull request with `onnx-needs-import`.

See [GitHub pull request workflow](https://docs.github.com/en/get-started/quickstart/github-flow).

Adhere to [Google's Code Review Developer Guide](https://google.github.io/eng-practices/review/).

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
python test/onnx/test_pytorch_onnx_onnxruntime.py TestONNXRuntime_opset11.test_arithmetic_prim_bool
# run test for opset 9
python test/onnx/test_pytorch_onnx_onnxruntime.py TestONNXRuntime.test_arithmetic_prim_bool
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

# Features

## Quantized model export

To support quantized model export, we need to unpack the quantized tensor inputs and the PackedParam weights (https://github.com/pytorch/pytorch/pull/69232). We construct through `TupleConstruct` to have a 1-to-1 input mapping, 
so that we can use `replaceAllUsesWith` API for its successors. In addition, we support quantized namespace export, and the developers can add more symbolics for quantized operators conveniently in the current framework.

# Updating default opset_version

```python
#!/usr/bin/env python

"""Outputs what the default value of opset_version should be.

The current policy is that the default should be set to the
latest released version as of 18 months ago.

Usage:
Only argument should be path to onnx git repo.
If run from the root of the PyTorch repo, run:
$ <this script> third_party/onnx
"""

import datetime
import os
import re
import sys
import subprocess

if len(sys.argv) != 2:
    sys.exit("need exactly 1 argument, the path to onnx git repo")

onnx_dir = sys.argv[1]
os.chdir(onnx_dir)

date = datetime.datetime.now() - datetime.timedelta(days=18*30)
onnx_commit = subprocess.check_output(("git", "log", f"--until={date}", "--max-count=1", "--format=%H"), encoding="utf-8").strip()
onnx_tags = subprocess.check_output(("git", "tag", "--list", f"--contains={onnx_commit}"), encoding="utf-8")
tag_tups = []
semver_pat = re.compile(r"v(\d+)\.(\d+)\.(\d+)")
for tag in onnx_tags.splitlines():
    match = semver_pat.match(tag)
    if match:
       tag_tups.append(tuple(int(x) for x in match.groups()))

min_tup = sorted(tag_tups)[0]
version_str = "{}.{}.{}".format(*min_tup)

head_commit = subprocess.check_output(("git", "log", "--max-count=1", "--format=%H", "HEAD"), encoding="utf-8").strip()

subprocess.check_call(("git", "checkout", f"v{version_str}"), stdout=subprocess.DEVNULL, stderr=subprocess.DEVNULL)
try:
    from onnx import helper
    for version in helper.VERSION_TABLE:
        if version[0] == version_str:
            print(version[2])
            sys.exit() # success
    sys.exit(f"failed to find version {version_str} in onnx.helper.VERSION_TABLE at commit {onnx_commit}")
finally:
    subprocess.check_call(("git", "checkout", head_commit), stdout=subprocess.DEVNULL, stderr=subprocess.DEVNULL)

```