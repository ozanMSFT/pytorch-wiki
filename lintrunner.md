PyTorch uses `lintrunner` to execute lints locally and in CI. This provides developers with a single command to run all the linters, and ensures consistency between the CI and local development environments. See [the `lintrunner` repo](https://github.com/suo/lintrunner) for more info.

To get started, run the following to install `lintrunner`. Make sure you are at the root of the PyTorch repo:
```
pip install lintrunner
lintrunner init
```
This will install `lintrunner` on your system and download all the necessary dependencies to run linters locally. Note that this will install new packages with `pip` and download binaries maintained by the PyTorch team. If you want to see what `lintrunner init` will install, run `lintrunner init --dry-run`.
### How do I run linters locally?
```
lintrunner
```
to lint your local changes (by default, this is your **working tree changes and the HEAD commit**).
### How do I autoformat my code?
```
lintrunner f
```
to format locally changed files.
### How do I run lint on my entire PR/branch?
If, say, your PR is targeting `master`, run:
```
lintrunner -m master
```
### How do I run lint on specific files?
```
lintrunner torch/jit/_script.py torch/jit/_trace.py
```
`xargs` also works fine:
```
find -type f torch/onnx | xargs lintrunner
```
### How do I run lint on all files?
```
lintrunner --all-files
```
**Warning**: this may take a while, PyTorch has a lot of files.
### How do I run only a specific linter?
```
lintrunner --take BLACK,CLANGFORMAT
```
To see what linter names to pass, consult the [config](https://github.com/pytorch/pytorch/blob/master/.lintrunner.toml). By default, `lintrunner` runs all linters. To skip a linter, use `--skip`.
### I have another question

For more docs run `lintrunner --help`, or see [the `lintrunner` repo](https://github.com/suo/lintrunner).