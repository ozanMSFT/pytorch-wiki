[[Page Maintainers|Where or how should I add documentation]]: [@abock](https://github.com/abock)

Documentation for developing the PyTorch-ONNX exporter (`torch.onnx`). For an index of all ONNX exporter related topics, see [[PyTorch ONNX Topics|PyTorch ONNX Topics]]

# Table of Contents

<!-- TOC generated with https://github.com/ekalinin/github-markdown-toc -->

- [Table of Contents](#table-of-contents)
- [Development process](#development-process)
  - [Environment setup](#environment-setup)
    - [Fork PyTorch](#fork-pytorch)
    - [Build PyTorch](#build-pytorch)
      - [Optional build tips](#optional-build-tips)
    - [Install additional dependencies](#install-additional-dependencies)
      - [ONNX and ONNX Runtime](#onnx-and-onnx-runtime)
      - [TorchVision](#torchvision)
    - [Sanity check](#sanity-check)
    - [VS Code](#vs-code)
      - [Recommended settings and extensions](#recommended-settings-and-extensions)
      - [Debugging with `gdb`](#debugging-with-gdb)
  - [Pull requests](#pull-requests)
  - [Tests](#tests)
- [Links](#links)
  - [Relevant parts of PyTorch repo](#relevant-parts-of-pytorch-repo)
- [Features](#features)
  - [Quantized model export](#quantized-model-export)

# Development process

## Environment setup

We highly recommend using Linux. Other platforms are not tested in PyTorch CI and
are generally not used by the `torch.onnx` developers.

### Fork PyTorch

[Fork](https://docs.github.com/en/get-started/quickstart/fork-a-repo) [github.com/pytorch/pytorch](https://github.com/pytorch/pytorch/fork) and clone your fork to your workstation.

### Build PyTorch

CUDA is not required for most development tasks. If you use CUDA, building PyTorch will probably be slower.

Install [Anaconda](https://www.anaconda.com/products/distribution#linux) and activate a new environment. Use Python 3.9 as `onnxruntime` does not support Python 3.10 or higher (as of April 2022).

Install [direnv](https://direnv.net/) and initialize your envrc file in the root of your PyTorch git repo:<br />
NOTE: Please remember to [hook installation](https://direnv.net/docs/hook.html) after you install direnv.
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

Install the dependencies required for development and to run CI checks locally.

```sh
pip install expecttest pytest parameterized flake8 hypothesis pytest-cov pytest-xdist pylint lintrunner ghstack beartype
lintrunner init
```

Read more about:
1. [[lintrunner|lintrunner]] (required): Run all the linters, and ensures consistency between the CI and local development environments.
2. [ghstack](https://github.com/ezyang/ghstack) (optional): Conveniently submit stacks of diffs to GitHub as separate pull requests.
NOTE: [GitLens](https://gitlens.amod.io/) comes in handy with ghstack
3. Recover your branch from ghstack: ghstack checkout github_link_to_pr


#### ONNX and ONNX Runtime

```sh
# install a version of protobuf compatible with ONNX submodule
conda install -c conda-forge \
  protobuf=$(cat third_party/onnx/requirements-release.txt | grep protobuf | awk '{print $3}') \
  python-flatbuffers
pip install onnxruntime onnx==$(cat third_party/onnx/VERSION_NUMBER)
```

> ONNX and ONNX Runtime are also available from conda-forge, but they depend on a version of protobuf that's newer than
> what the ONNX submodule wants to use, which lead to seg-faults in my case. This may be resolved with future
> versions of ONNX or ONNX Runtime. If you find `conda install -c conda-forge onnxruntime` works, please update
> these instructions to use conda instead of pip.

#### TorchVision

The ONNX tests depend on torchvision.
This is tricky because TorchVision depends on PyTorch, but we don't want our
package manager to install PyTorch, we want to use our locally built one.
The best solution I've found is to install torchvision with pip (so that
conda doesn't try to manage it) without any deps (so that pip doesn't install
pytorch and the locally built version is used).

```sh
# If you're not using CUDA, use the command below. If you are, see https://pytorch.org/get-started/locally/
pip install --upgrade --no-deps --pre torchvision --extra-index-url https://download.pytorch.org/whl/nightly/cpu
# manually install torchvision deps
conda install -c conda-forge pillow
```

> I hope there's a better way to deal with this. If you know of one please
> update these instructions and email the team!

### Sanity check

You should be able to run these commands successfully:

```sh
python setup.py develop
pytest -svk test_arithmetic_prim_long test/onnx/test_pytorch_onnx_onnxruntime.py
```

And this should fail:

```sh
echo "assert False" >> torch/onnx/utils.py
pytest -svk test_arithmetic_prim_long test/onnx/test_pytorch_onnx_onnxruntime.py
git restore torch/onnx/utils.py
```

If the second command succeeds, then probably python is finding a PyTorch that was installed via `conda` or `pip`, not the one that was built from source by `python setup.py develop`.

### VS Code

#### Recommended settings and extensions

You can place this recommended `settings.json` under `.vscode/`

```jsonc
{
    // Always remove trailing whitespaces
    "files.trimTrailingWhitespace": true,
    "files.insertFinalNewline": true,
    "files.trimFinalNewlines": true,
    "[python]": {
        "editor.tabSize": 4,
        // Set to true to auto sort imports
        "editor.codeActionsOnSave": {
            "source.organizeImports": false
        },
        "editor.rulers": [88],
    },
    // Enable Python linting and Pylance type checking
    "python.analysis.typeCheckingMode": "basic",
    "python.formatting.provider": "black",
    "python.sortImports.args": ["--profile", "black"],
    "python.linting.enabled": true,
    "python.linting.flake8Enabled": true,
    "python.linting.pydocstyleEnabled": true,
    "python.linting.pydocstyleArgs": ["--convention=google"],
    "python.linting.banditEnabled": true,
    "python.linting.pylintEnabled": true,
    "python.linting.pylintArgs": [
        "--disable=no-member"
    ]
}
```

Recommended extensions (you can install them in the "Extensions" tab)

```jsonc
{
  "recommendations": [
    // Python
    "ms-python.python",
    "ms-python.vscode-pylance",
    "njpwerner.autodocstring",
    // Markdown
    "yzhang.markdown-all-in-one",
    "DavidAnson.vscode-markdownlint",
    // Coding style
    "shardulm94.trailing-spaces",
    // Linting display
    "usernamehw.errorlens",
    "igorsbitnev.error-gutters",
    "ryanluker.vscode-coverage-gutters",
    // Github review integration
    "GitHub.vscode-pull-request-github",
    "eamodio.gitlens",
    // Show changed files between branches
    "letmaik.git-tree-compare",
  ]
}
```

If you use error lens, I recommend the following settings

```jsonc
{
    "errorLens.excludeBySource": [
        "cSpell" // Exclude noisy spelling errors
    ],
    "errorLens.followCursor": "closestProblem",
    "errorLens.fontSize": "0.9em", // Smaller unintrusive messages
    "errorLens.followCursorMore": 3, // Hide errors too far away from the cursor
}
```

#### Debugging with `gdb`

You can set up VS Code to run `gdb` and set breakpoints when debugging c++ code. In `launch.json` add the configuration

```jsonc
  // ...
  "configurations": [
    {
      "name": "(gdb) Launch",
      "type": "cppdbg",
      "request": "launch",
      "program": "<path to python bin>",
      "args": [
        "-m",
        "pytest",
        "<test file and test name in pytest format>"
      ],
      "stopAtEntry": false,
      "cwd": "path/to/repo/of/pytorch",
      "environment": [],
      "externalConsole": false,
      "MIMode": "gdb",
      "setupCommands": [
        {
          "description": "Enable pretty-printing for gdb",
          "text": "-enable-pretty-printing",
          "ignoreFailures": true
        },
        {
          "description": "Set Disassembly Flavor to Intel",
          "text": "-gdb-set disassembly-flavor intel",
          "ignoreFailures": true
        }
      ]
    },
  ]
```

You can then set breakpoints in the c++ source and run the debugger in VS Code.

## Pull requests

PRs should be opened directly against master. PRs can be directly merged into master as long as it satisfies the [ONNX merge rule](https://github.com/pytorch/pytorch/blob/master/.github/merge_rules.yaml#L1):

* Approved by one of torch.onnx developers listed in `approved_by` section.
* All modified files fall under the `patterns` section.

Pay special attention to the following GitHub checks:

* Has "onnx" in the name, which runs ONNX related tests.
* Has "Lint" in the name, which does code format checks.

Regarding other failing checks: if you are certain the failure is unrelated to your change, try rebasing on master. Often these failures are caused by a branch being out of sync with master.
You can ignore the failing check if it is a regression in master. This can be verified by checking if master is also failing from [CI HUD](https://hud.pytorch.org/ci/pytorch/pytorch/master).

**To merge your pull request, comment on the PR `@pytorchbot merge`.** (doc [[Bot commands|]])

If you make changes to non-ONNX related code, i.e. files outside of [ONNX merge rule](https://github.com/pytorch/pytorch/blob/master/.github/merge_rules.yaml#L1), please note the PR will require additional reviews from people outside of torch.onnx developers, and will take a longer process to merge into master. In this case, pytorchbot will not be able to merge the pull request. It will leave a comment like "Merge failed due to PR XXX does not match merge rules". Please label the pull request with `onnx-needs-import`.

See [GitHub pull request workflow](https://docs.github.com/en/get-started/quickstart/github-flow).

Adhere to [Google's Code Review Developer Guide](https://google.github.io/eng-practices/review/) and [[PyTorch Code review values|Code review values]]

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

Tests added to `TestONNXRuntime` are automatically defined for all supported opset versions. Use the `-k` option in pytest to run the test you want.

For example:

```sh
# run the `test_quantized_arithmetic_qfunctional` test
python -m pytest test/onnx/test_pytorch_onnx_onnxruntime.py -k test_quantized_arithmetic_qfunctional
```

An example of adding unit tests for a new symbolic function: [Add binary_cross_entropy_with_logits op](https://github.com/pytorch/pytorch/pull/49675)

You can use `pytest` to run tests in parallel and generate a coverage report.

```sh
python -m pytest -n auto --cov --cov-report "xml:test/coverage.xml" test/onnx/test_pytorch_onnx_onnxruntime.py
```

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

To support quantized model export, we need to unpack the quantized tensor inputs and the PackedParam weights (<https://github.com/pytorch/pytorch/pull/69232>). We construct through `TupleConstruct` to have a 1-to-1 input mapping,
so that we can use `replaceAllUsesWith` API for its successors. In addition, we support quantized namespace export, and the developers can add more symbolics for quantized operators conveniently in the current framework.