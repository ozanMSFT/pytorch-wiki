PyTorch uses `lintrunner` to execute lints locally and in CI. This provides developers with a single command to run all the linters, and ensures consistency between the CI and local development environments. See [the `lintrunner` repo](https://github.com/suo/lintrunner) for more info.

To get started, run the following to install `lintrunner`. Make sure you are at the root of the PyTorch repo:
```
pip install lintrunner
lintrunner init
```
This will install `lintrunner` on your system and download all the necessary dependencies to run linters locally. Note that this will install new packages with `pip` and download binaries maintained by the PyTorch team. If you want to see what `lintrunner init` will install, run `lintrunner init --dry-run`.

After that, just run `lintrunner` to lint your changes! For more docs, see [the `lintrunner` repo](https://github.com/suo/lintrunner).