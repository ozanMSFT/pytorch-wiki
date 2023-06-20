# Tips & Debugging

If you are working on the C++ code, there are a few important things that you will want to keep in mind:
    1. How to rebuild only the code you are working on.
    2. How to make rebuilds in the absence of changes go faster. 


## Selective Rebuild

### Use build options
See [Install-Build.md#build-only-what-you-need] for building specific components.

### Reduce reinstalls
* One way to avoid running `python setup.py develop` every time one makes a change to C++/CUDA/ObjectiveC files on Linux/Mac, is to create a symbolic link from `build` folder to `torch/lib`, for example, by issuing following:
    ```bash
    pushd torch/lib; sh -c "ln -sf ../../build/lib/libtorch_cpu.* ."; popd
    ```
    Afterwards rebuilding a library (for example to rebuild `libtorch_cpu.so` issue `ninja torch_cpu` from `build` folder), would be sufficient to make change visible in `torch` package.

### Clean reinstall
* To reinstall, first uninstall all existing PyTorch installs. You may need to run `pip uninstall torch` multiple times. You'll know `torch` is fully
uninstalled when you see `WARNING: Skipping torch as it is not installed`. (You should only have to `pip uninstall` a few times, but you can always `uninstall` with `timeout` or in a loop if you're feeling lazy.)

  ```bash
  conda uninstall pytorch -y
  yes | pip uninstall torch
  ```

  Next run `python setup.py clean`. After that, you can install in `develop` mode again.

## Faster Rebuild

### Make no-op build fast

#### Use Ninja

By default, cmake will use its Makefile generator to generate your build
system.  You can get faster builds if you install the ninja build system
with `pip install ninja`.  If PyTorch was already built, you will need
to run `python setup.py clean` once after installing ninja for builds to
succeed.

#### Use CCache

Even when dependencies are tracked with file modification, there are many
situations where files get rebuilt when a previous compilation was exactly the
same. Using ccache in a situation like this is a real time-saver.

Before building pytorch, install ccache from your package manager of choice:

```bash
conda install ccache -c conda-forge
sudo apt install ccache
sudo yum install ccache
brew install ccache
```

You may also find the default cache size in ccache is too small to be useful.
The cache sizes can be increased from the command line:

```bash
# config: cache dir is ~/.ccache, conf file ~/.ccache/ccache.conf
# max size of cache
ccache -M 25Gi  # -M 0 for unlimited
# unlimited number of files
ccache -F 0
```

To check this is working, do two clean builds of pytorch in a row. The second
build should be substantially and noticeably faster than the first build. If
this doesn't seem to be the case, check the `CMAKE_<LANG>_COMPILER_LAUNCHER`
rules in `build/CMakeCache.txt`, where `<LANG>` is `C`, `CXX` and `CUDA`.
Each of these 3 variables should contain ccache, e.g.

```
//CXX compiler launcher
CMAKE_CXX_COMPILER_LAUNCHER:STRING=/usr/bin/ccache
```

If not, you can define these variables on the command line before invoking `setup.py`.

```bash
export CMAKE_C_COMPILER_LAUNCHER=ccache
export CMAKE_CXX_COMPILER_LAUNCHER=ccache
export CMAKE_CUDA_COMPILER_LAUNCHER=ccache
python setup.py develop
```

#### Use a faster linker

If you are editing a single file and rebuilding in a tight loop, the time spent
linking will dominate. The system linker available in most Linux distributions
(GNU `ld`) is quite slow. Use a faster linker, like [lld](https://lld.llvm.org/).

The easiest way to use `lld` this is download the
[latest LLVM binaries](http://releases.llvm.org/download.html#8.0.0) and run:

```bash
ln -s /path/to/downloaded/ld.lld /usr/local/bin/ld
```

Mac users should follow [this guide](https://stackoverflow.com/questions/42730345/how-to-install-llvm-for-mac) instead.

#### Use pre-compiled headers

Sometimes there's no way of getting around rebuilding lots of files, for example
editing `native_functions.yaml` usually means 1000+ files being rebuilt. If
you're using CMake newer than 3.16, you can enable pre-compiled headers by
setting `USE_PRECOMPILED_HEADERS=1` either on first setup, or in the
`CMakeCache.txt` file.

```sh
USE_PRECOMPILED_HEADERS=1 python setup.py develop
```

This adds a build step where the compiler takes `<ATen/ATen.h>` and essentially
dumps it's internal AST to a file so the compiler can avoid repeating itself for
every `.cpp` file.

One caveat is that when enabled, this header gets included in every file by default.
Which may change what code is legal, for example:
- internal functions can never alias existing names in `<ATen/ATen.h>`
- names in `<ATen/ATen.h>` will work even if you don't explicitly include it.

#### Workaround for header dependency bug in nvcc
If re-building without modifying any files results in several CUDA files being
re-compiled, you may be running into an `nvcc` bug where header dependencies are
not converted to absolute paths before reporting it to the build system. This
makes `ninja` think one of the header files has been deleted, so it runs the
build again.

A compiler-wrapper to fix this is provided in `tools/nvcc_fix_deps.py`. You can use
this as a compiler launcher, similar to `ccache`
```bash
export CMAKE_CUDA_COMPILER_LAUNCHER="python;`pwd`/tools/nvcc_fix_deps.py;ccache"
python setup.py develop
```

---

## General Debugging

* If you run into errors when running `python setup.py develop`, here are some debugging steps:
  1. Run `printf '#include <stdio.h>\nint main() { printf("Hello World");}'|clang -x c -; ./a.out` to make sure
  your CMake works and can compile this simple Hello World program without errors.
  2. Nuke your `build` directory. The `setup.py` script compiles binaries into the `build` folder and caches many
  details along the way, which saves time the next time you build. If you're running into issues, you can always
  `rm -rf build` from the toplevel `pytorch` directory and start over.
  3. If you have made edits to the PyTorch repo, commit any change you'd like to keep and clean the repo with the
  following commands (note that clean _really_ removes all untracked files and changes.):
      ```bash
      git submodule deinit -f .
      git clean -xdf
      python setup.py clean
      git submodule update --init --recursive # very important to sync the submodules
      python setup.py develop                 # then try running the command again
      ```
  4. The main step within `python setup.py develop` is running `make` from the `build` directory. If you want to
    experiment with some environment variables, you can pass them into the command:
      ```bash
      ENV_KEY1=ENV_VAL1[, ENV_KEY2=ENV_VAL2]* python setup.py develop
      ```

* If you run into issue running `git submodule update --init --recursive`. Please try the following:
  - If you encounter an error such as
    ```
    error: Submodule 'third_party/pybind11' could not be updated
    ```
    check whether your Git local or global config file contains any `submodule.*` settings. If yes, remove them and try again.
    (please reference [this doc](https://git-scm.com/docs/git-config#Documentation/git-config.txt-submoduleltnamegturl) for more info).

  - If you encounter an error such as
    ```
    fatal: unable to access 'https://github.com/pybind11/pybind11.git': could not load PEM client certificate ...
    ```
    this is likely that you are using HTTP proxying and the certificate expired. To check if the certificate is valid, run
    `git config --global --list` and search for config like `http.proxysslcert=<cert_file>`. Then check certificate valid date by running
    ```bash
    openssl x509 -noout -in <cert_file> -dates
    ```

  - If you encounter an error that some third_party modules are not checked out correctly, such as
    ```
    Could not find .../pytorch/third_party/pybind11/CMakeLists.txt
    ```
    remove any `submodule.*` settings in your local git config (`.git/config` of your pytorch repo) and try again.
* If you're a Windows contributor, please check out [Best Practices](https://github.com/pytorch/pytorch/wiki/Best-Practices-to-Edit-and-Compile-Pytorch-Source-Code-On-Windows).
* For help with any part of the contributing process, please don’t hesitate to utilize our Zoom office hours! See details [here](https://github.com/pytorch/pytorch/wiki/Dev-Infra-Office-Hours)


## Uncategorized 1

You can adjust the configuration of cmake variables optionally (without building first), by doing
the following. For example, adjusting the pre-detected directories for CuDNN or BLAS can be done
with such a step.

On Linux
```bash
export CMAKE_PREFIX_PATH=${CONDA_PREFIX:-"$(dirname $(which conda))/../"}
python setup.py build --cmake-only
ccmake build  # or cmake-gui build
```

On macOS
```bash
export CMAKE_PREFIX_PATH=${CONDA_PREFIX:-"$(dirname $(which conda))/../"}
MACOSX_DEPLOYMENT_TARGET=10.9 CC=clang CXX=clang++ python setup.py build --cmake-only
ccmake build  # or cmake-gui build
```

## Uncategorized 2
When installing with `python setup.py develop` (in contrast to `python setup.py install`) Python runtime will use the current local source-tree when importing `torch` package. (This is done by creating [`.egg-link`](https://wiki.python.org/moin/PythonPackagingTerminology#egg-link) file in `site-packages` folder). This way you do not need to repeatedly install after modifying Python files (`.py`). However, you would need to reinstall if you modify Python interface (`.pyi`, `.pyi.in`) or non-Python files (`.cpp`, `.cc`, `.cu`, `.h`, ...).






