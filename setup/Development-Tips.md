## General tips and tricks
* If you want to have no-op incremental rebuilds (which are fast), see [Make no-op build fast](#make-no-op-build-fast) below.


* If you don't need CUDA, build using USE_CUDA=0: the build is significantly faster. There are also a lot of other build flags that help get rid of components that you might not work on. Below is an opinionated build command that gets rid of a lot of different options that don't get used very often.

  `USE_KINETO=0 BUILD_CAFFE2=0 USE_DISTRIBUTED=0 USE_NCCL=0 BUILD_TEST=0 USE_XNNPACK=0 USE_FBGEMM=0 USE_QNNPACK=0 USE_MKLDNN=0 USE_MIOPEN=0 USE_NNPACK=0 BUILD_CAFFE2_OPS=0 USE_TENSORPIPE=0 python setup.py develop`

  See [Build only what you need](#build-only-what-you-need) for a list of useful build flags.

* When developing PyTorch, instead of branching off of `master`, you can branch off of `viable/strict`. `viable/strict` is a branch that lags behind master and guarantees that all PyTorch tests are passing on the branch. Basing your work off of `viable/strict` gives you confidence that any test failures are actually your code's fault.

  ```
  # Creating a new feature branch off of viable/strict
  git checkout viable/strict
  git checkout -b my_new_feature

  # Rebasing your work to appear on top of viable/strict, assuming upstream points to pytorch/pytorch.
  # (Some people develop with origin pointing to pytorch/pytorch)
  git pull --rebase upstream viable/strict
  ```

## Build only what you need

`python setup.py develop` will build everything by default, but sometimes you are
only interested in a specific component.

- Working on a test binary? Run `(cd build && ninja bin/test_binary_name)` to
  rebuild only that test binary (without rerunning cmake). (Replace `ninja` with
  `make` if you don't have ninja installed).
- Don't need Caffe2?  Pass `BUILD_CAFFE2=0` to disable Caffe2 build.

On the initial build, you can also speed things up with the environment
variables `DEBUG`, `USE_DISTRIBUTED`, `USE_MKLDNN`, `USE_CUDA`, `BUILD_TEST`, `USE_FBGEMM`, `USE_NNPACK` and `USE_QNNPACK`.

- `DEBUG=1` will enable debug builds (-g -O0)
- `REL_WITH_DEB_INFO=1` will enable debug symbols with optimizations (-g -O3)
- `USE_DISTRIBUTED=0` will disable distributed (c10d, gloo, mpi, etc.) build.
- `USE_MKLDNN=0` will disable using MKL-DNN.
- `USE_CUDA=0` will disable compiling CUDA (in case you are developing on something not CUDA related), to save compile time.
- `BUILD_TEST=0` will disable building C++ test binaries.
- `USE_FBGEMM=0` will disable using FBGEMM (quantized 8-bit server operators).
- `USE_NNPACK=0` will disable compiling with NNPACK.
- `USE_QNNPACK=0` will disable QNNPACK build (quantized 8-bit operators).
- `USE_XNNPACK=0` will disable compiling with XNNPACK.

For example:

```bash
DEBUG=1 USE_DISTRIBUTED=0 USE_MKLDNN=0 USE_CUDA=0 BUILD_TEST=0 USE_FBGEMM=0 USE_NNPACK=0 USE_QNNPACK=0 USE_XNNPACK=0 python setup.py develop
```

For subsequent builds (i.e., when `build/CMakeCache.txt` exists), the build
options passed for the first time will persist; please run `ccmake build/`, run
`cmake-gui build/`, or directly edit `build/CMakeCache.txt` to adapt build
options.

## Reduce reinstalls
When installing with `python setup.py develop` (in contrast to `python setup.py install`) Python runtime will use
the current local source-tree when importing `torch` package. (This is done by creating [`.egg-link`](https://wiki.python.org/moin/PythonPackagingTerminology#egg-link) file in `site-packages` folder)
This way you do not need to repeatedly install after modifying Python files (`.py`).
However, you would need to reinstall if you modify Python interface (`.pyi`, `.pyi.in`) or
non-Python files (`.cpp`, `.cc`, `.cu`, `.h`, ...).

One way to avoid running `python setup.py develop` every time one makes a change to C++/CUDA/ObjectiveC files on Linux/Mac, is to create a symbolic link from `build` folder to `torch/lib`, for example, by issuing following:
    ```bash
    pushd torch/lib; sh -c "ln -sf ../../build/lib/libtorch_cpu.* ."; popd
    ```
    Afterwards rebuilding a library (for example to rebuild `libtorch_cpu.so` issue `ninja torch_cpu` from `build` folder), would be sufficient to make change visible in `torch` package.


## C++ development tips

If you are working on the C++ code, there are a few important things that you
will want to keep in mind:

1. How to rebuild only the code you are working on.
2. How to make rebuilds in the absence of changes go faster.
### Code completion and IDE support

When using `python setup.py develop`, PyTorch will generate
a `compile_commands.json` file that can be used by many editors
to provide command completion and error highlighting for PyTorch's
C++ code. You need to `pip install ninja` to generate accurate
information for the code in `torch/csrc`. More information at:
- https://sarcasm.github.io/notes/dev/compilation-database.html

### Make no-op builds fast

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

People on Mac, follow [this guide](https://stackoverflow.com/questions/42730345/how-to-install-llvm-for-mac) instead.

The easiest way to use `lld` this is download the
[latest LLVM binaries](http://releases.llvm.org/download.html#8.0.0) and run:

```bash
ln -s /path/to/downloaded/ld.lld /usr/local/bin/ld
```

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

### C++ frontend development tips

We have very extensive tests in the [test/cpp/api](test/cpp/api) folder. The
tests are a great way to see how certain components are intended to be used.
When compiling PyTorch from source, the test runner binary will be written to
`build/bin/test_api`. The tests use the [GoogleTest](https://github.com/google/googletest/blob/master/googletest)
framework, which you can read up about to learn how to configure the test runner. When
submitting a new feature, we care very much that you write appropriate tests.
Please follow the lead of the other tests to see how to write a new test case.

### GDB integration

If you are debugging pytorch inside GDB, you might be interested in
[pytorch-gdb](tools/gdb/pytorch-gdb.py). This script introduces some
pytorch-specific commands which you can use from the GDB prompt. In
particular, `torch-tensor-repr` prints a human-readable repr of an at::Tensor
object. Example of usage:

```
$ gdb python
GNU gdb (GDB) 9.2
[...]
(gdb) # insert a breakpoint when we call .neg()
(gdb) break at::Tensor::neg
Function "at::Tensor::neg" not defined.
Make breakpoint pending on future shared library load? (y or [n]) y
Breakpoint 1 (at::Tensor::neg) pending.

(gdb) run
[...]
>>> import torch
>>> t = torch.tensor([1, 2, 3, 4], dtype=torch.float64)
>>> t
tensor([1., 2., 3., 4.], dtype=torch.float64)
>>> t.neg()

Thread 1 "python" hit Breakpoint 1, at::Tensor::neg (this=0x7ffb118a9c88) at aten/src/ATen/core/TensorBody.h:3295
3295    inline at::Tensor Tensor::neg() const {
(gdb) # the default repr of 'this' is not very useful
(gdb) p this
$1 = (const at::Tensor * const) 0x7ffb118a9c88
(gdb) p *this
$2 = {impl_ = {target_ = 0x55629b5cd330}}
(gdb) torch-tensor-repr *this
Python-level repr of *this:
tensor([1., 2., 3., 4.], dtype=torch.float64)
```

GDB tries to automatically load `pytorch-gdb` thanks to the
[.gdbinit](.gdbinit) at the root of the pytorch repo. However, auto-loadings is disabled by default, because of security reasons:

```bash
$ gdb
warning: File "/path/to/pytorch/.gdbinit" auto-loading has been declined by your `auto-load safe-path' set to "$debugdir:$datadir/auto-load".
To enable execution of this file add
        add-auto-load-safe-path /path/to/pytorch/.gdbinit
line to your configuration file "/home/YOUR-USERNAME/.gdbinit".
To completely disable this security protection add
        set auto-load safe-path /
line to your configuration file "/home/YOUR-USERNAME/.gdbinit".
For more information about this security protection see the
"Auto-loading safe path" section in the GDB manual.  E.g., run from the shell:
        info "(gdb)Auto-loading safe path"
(gdb)
```

As gdb itself suggests, the best way to enable auto-loading of `pytorch-gdb`
is to add the following line to your `~/.gdbinit` (i.e., the `.gdbinit` file
which is in your home directory, **not** `/path/to/pytorch/.gdbinit`):

```bash
add-auto-load-safe-path /path/to/pytorch/.gdbinit
```

### C++ stacktraces
Set `TORCH_SHOW_CPP_STACKTRACES=1` to get the C++ stacktrace when an error occurs in Python.

## CUDA development tips

If you are working on the CUDA code, here are some useful CUDA debugging tips:

1. `CUDA_DEVICE_DEBUG=1` will enable CUDA device function debug symbols (`-g -G`).
    This will be particularly helpful in debugging device code. However, it will
    slow down the build process for about 50% (compared to only `DEBUG=1`), so use wisely.
2. `cuda-gdb` and `cuda-memcheck` are your best CUDA debugging friends. Unlike`gdb`,
   `cuda-gdb` can display actual values in a CUDA tensor (rather than all zeros).
3. CUDA supports a lot of C++11/14 features such as, `std::numeric_limits`, `std::nextafter`,
   `std::tuple` etc. in device code. Many of such features are possible because of the
   [--expt-relaxed-constexpr](https://docs.nvidia.com/cuda/cuda-c-programming-guide/index.html#constexpr-functions)
   nvcc flag. There is a known [issue](https://github.com/ROCm-Developer-Tools/HIP/issues/374)
   that ROCm errors out on device code, which uses such stl functions.
4. A good performance metric for a CUDA kernel is the
   [Effective Memory Bandwidth](https://devblogs.nvidia.com/how-implement-performance-metrics-cuda-cc/).
   It is useful for you to measure this metric whenever you are writing/optimizing a CUDA
   kernel. Following script shows how we can measure the effective bandwidth of CUDA `uniform_`
   kernel.
   ```python
   import torch
   from torch.utils.benchmark import Timer
   size = 128*512
   nrep = 100
   nbytes_read_write = 4 # this is number of bytes read + written by a kernel. Change this to fit your kernel.

   for i in range(10):
       a=torch.empty(size).cuda().uniform_()
       torch.cuda.synchronize()
       out = a.uniform_()
       torch.cuda.synchronize()
       t = Timer(stmt="a.uniform_()", globals=globals())
       res = t.blocked_autorange()
       timec = res.median
       print("uniform, size, elements", size, "forward", timec, "bandwidth (GB/s)", size*(nbytes_read_write)*1e-9/timec)
       size *=2
   ```

  See more cuda development tips [here](https://github.com/pytorch/pytorch/wiki/CUDA-basics)

## Windows development tips

For building from source on Windows, consult
[our documentation](https://pytorch.org/docs/stable/notes/windows.html) on it.

Occasionally, you will write a patch which works on Linux, but fails CI on Windows.
There are a few aspects in which MSVC (the Windows compiler toolchain we use) is stricter
than Linux, which are worth keeping in mind when fixing these problems.

1. Symbols are NOT exported by default on Windows; instead, you have to explicitly
   mark a symbol as exported/imported in a header file with `__declspec(dllexport)` /
   `__declspec(dllimport)`. We have codified this pattern into a set of macros
   which follow the convention `*_API`, e.g., `TORCH_API` inside Caffe2, Aten and Torch.
   (Every separate shared library needs a unique macro name, because symbol visibility
   is on a per shared library basis. See c10/macros/Macros.h for more details.)

   The upshot is if you see an "unresolved external" error in your Windows build, this
   is probably because you forgot to mark a function with `*_API`. However, there is
   one important counterexample to this principle: if you want a *templated* function
   to be instantiated at the call site, do NOT mark it with `*_API` (if you do mark it,
   you'll have to explicitly instantiate all of the specializations used by the call
   sites.)

2. If you link against a library, this does not make its dependencies transitively
   visible. You must explicitly specify a link dependency against every library whose
   symbols you use. (This is different from Linux where in most environments,
   transitive dependencies can be used to fulfill unresolved symbols.)

3. If you have a Windows box (we have a few on EC2 which you can request access to) and
   you want to run the build, the easiest way is to just run `.ci/pytorch/win-build.sh`.
   If you need to rebuild, run `REBUILD=1 .ci/pytorch/win-build.sh` (this will avoid
   blowing away your Conda environment.)

Even if you don't know anything about MSVC, you can use cmake to build simple programs on
Windows; this can be helpful if you want to learn more about some peculiar linking behavior
by reproducing it on a small example. Here's a simple example cmake file that defines
two dynamic libraries, one linking with the other:

```CMake
project(myproject CXX)
set(CMAKE_CXX_STANDARD 14)
add_library(foo SHARED foo.cpp)
add_library(bar SHARED bar.cpp)
# NB: don't forget to __declspec(dllexport) at least one symbol from foo,
# otherwise foo.lib will not be created.
target_link_libraries(bar PUBLIC foo)
```

You can build it with:

```bash
mkdir build
cd build
cmake ..
cmake --build .
```

### Known MSVC (and MSVC with NVCC) bugs

The PyTorch codebase sometimes likes to use exciting C++ features, and
these exciting features lead to exciting bugs in Windows compilers.
To add insult to injury, the error messages will often not tell you
which line of code actually induced the erroring template instantiation.

We've found the most effective way to debug these problems is to
carefully read over diffs, keeping in mind known bugs in MSVC/NVCC.
Here are a few well known pitfalls and workarounds:

* This is not actually a bug per se, but in general, code generated by MSVC
  is more sensitive to memory errors; you may have written some code
  that does a use-after-free or stack overflows; on Linux the code
  might work, but on Windows your program will crash. ASAN may not
  catch all of these problems: stay vigilant to the possibility that
  your crash is due to a real memory problem.

* (NVCC) `c10::optional` does not work when used from device code. Don't use
  it from kernels. Upstream issue: https://github.com/akrzemi1/Optional/issues/58
  and our local issue #10329.

* `constexpr` generally works less well on MSVC.

  * The idiom `static_assert(f() == f())` to test if `f` is constexpr
    does not work; you'll get "error C2131: expression did not evaluate
    to a constant". Don't use these asserts on Windows.
    (Example: `c10/util/intrusive_ptr.h`)

* (NVCC) Code you access inside a `static_assert` will eagerly be
  evaluated as if it were device code, and so you might get an error
  that the code is "not accessible".

```cpp
class A {
  static A singleton_;
  static constexpr inline A* singleton() {
    return &singleton_;
  }
};
static_assert(std::is_same(A*, decltype(A::singleton()))::value, "hmm");
```

* The compiler will run out of heap space if you attempt to compile files that
  are too large. Splitting such files into separate files helps.
  (Example: `THTensorMath`, `THTensorMoreMath`, `THTensorEvenMoreMath`.)

* MSVC's preprocessor (but not the standard compiler) has a bug
  where it incorrectly tokenizes raw string literals, ending when it sees a `"`.
  This causes preprocessor tokens inside the literal like an`#endif`  to be incorrectly
  treated as preprocessor directives. See https://godbolt.org/z/eVTIJq as an example.

* Either MSVC or the Windows headers have a PURE macro defined and will replace
  any occurrences of the PURE token in code with an empty string. This is why
  we have AliasAnalysisKind::PURE_FUNCTION and not AliasAnalysisKind::PURE.
  The same is likely true for other identifiers that we just didn't try to use yet.

### Building on legacy code and CUDA

CUDA, MSVC, and PyTorch versions are interdependent; please install matching versions from this table:
| CUDA version | Newest supported VS version                             | PyTorch version |
| ------------ | ------------------------------------------------------- | --------------- |
| 10.1         | Visual Studio 2019 (16.X) (`_MSC_VER` < 1930)           |  1.3.0 ~ 1.7.0  |
| 10.2         | Visual Studio 2019 (16.X) (`_MSC_VER` < 1930)           |  1.5.0 ~ 1.7.0  |
| 11.0         | Visual Studio 2019 (16.X) (`_MSC_VER` < 1930)           |      1.7.0      |

Note: There's a [compilation issue](https://github.com/oneapi-src/oneDNN/issues/812) in several Visual Studio 2019 versions since 16.7.1, so please make sure your Visual Studio 2019 version is not in 16.7.1 ~ 16.7.5

## Building PyTorch with ASAN

[ASAN](https://github.com/google/sanitizers/wiki/AddressSanitizer) is very
useful for debugging memory errors in C++. We run it in CI, but here's how to
get the same thing to run on your local machine.

First, install LLVM 8. The easiest way is to get [prebuilt
binaries](http://releases.llvm.org/download.html#8.0.0) and extract them to
folder (later called `$LLVM_ROOT`).

Then set up the appropriate scripts. You can put this in your `.bashrc`:

```bash
LLVM_ROOT=<wherever your llvm install is>
PYTORCH_ROOT=<wherever your pytorch checkout is>

LIBASAN_RT="$LLVM_ROOT/lib/clang/8.0.0/lib/linux/libclang_rt.asan-x86_64.so"
build_with_asan()
{
  LD_PRELOAD=${LIBASAN_RT} \
  CC="$LLVM_ROOT/bin/clang" \
  CXX="$LLVM_ROOT/bin/clang++" \
  LDSHARED="clang --shared" \
  LDFLAGS="-stdlib=libstdc++" \
  CFLAGS="-fsanitize=address -fno-sanitize-recover=all -shared-libasan -pthread" \
  CXX_FLAGS="-pthread" \
  USE_CUDA=0 USE_OPENMP=0 BUILD_CAFFE2_OPS=0 USE_DISTRIBUTED=0 DEBUG=1 \
  python setup.py develop
}

run_with_asan()
{
  LD_PRELOAD=${LIBASAN_RT} $@
}

# you can look at build-asan.sh to find the latest options the CI uses
export ASAN_OPTIONS=detect_leaks=0:symbolize=1:strict_init_order=true
export UBSAN_OPTIONS=print_stacktrace=1:suppressions=$PYTORCH_ROOT/ubsan.supp
export ASAN_SYMBOLIZER_PATH=$LLVM_ROOT/bin/llvm-symbolizer
```

Then you can use the scripts like:

```
suo-devfair ~/pytorch ❯ build_with_asan
suo-devfair ~/pytorch ❯ run_with_asan python test/test_jit.py
```

### Getting `ccache` to work

The scripts above specify the `clang` and `clang++` binaries directly, which
bypasses `ccache`. Here's how to get `ccache` to work:

1. Make sure the ccache symlinks for `clang` and `clang++` are set up (see
   CONTRIBUTING.md)
2. Make sure `$LLVM_ROOT/bin` is available on your `$PATH`.
3. Change the `CC` and `CXX` variables in `build_with_asan()` to point
   directly to `clang` and `clang++`.

### Why this stuff with `LD_PRELOAD` and `LIBASAN_RT`?

The “standard” workflow for ASAN assumes you have a standalone binary:

1. Recompile your binary with `-fsanitize=address`.
2. Run the binary, and ASAN will report whatever errors it find.

Unfortunately, PyTorch is a distributed as a shared library that is loaded by
a third-party executable (Python). It’s too much of a hassle to recompile all
of Python every time we want to use ASAN. Luckily, the ASAN folks have a
workaround for cases like this:

1. Recompile your library with `-fsanitize=address -shared-libasan`. The
   extra `-shared-libasan` tells the compiler to ask for the shared ASAN
   runtime library.
2. Use `LD_PRELOAD` to tell the dynamic linker to load the ASAN runtime
   library before anything else.

More information can be found
[here](https://github.com/google/sanitizers/wiki/AddressSanitizerAsDso).

### Why LD_PRELOAD in the build function?

We need `LD_PRELOAD` because there is a cmake check that ensures that a
simple program builds and runs. If we are building with ASAN as a shared
library, we need to `LD_PRELOAD` the runtime library, otherwise there will
dynamic linker errors and the check will fail.

We don’t actually need either of these if we fix the cmake checks.

### Why no leak detection?

Python leaks a lot of memory. Possibly we could configure a suppression file,
but we haven’t gotten around to it.

