
# Install & Build

Source: https://github.com/pytorch/pytorch#from-source

Before you build, take a look at some tips that make building faster. You will need to do them before you build PyTorch.

## Install from Source
Now that you have the source code, you can build PyTorch on your development machine. Note that the steps might be different depending on your machine's platform.

## ** !!! when should I use build/develop/install !!! **

### MacOS

```bash
python3 setup.py develop
```

### Linux

If you would like to compile PyTorch with [new C++ ABI](https://gcc.gnu.org/onlinedocs/libstdc++/manual/using_dual_abi.html) enabled, then first run this command:
```bash
export _GLIBCXX_USE_CXX11_ABI=1
```

If you're compiling for AMD ROCm then first run this command:
```bash
# Only run this if you're compiling for ROCm
python tools/amd_build/build_amd.py
```

Install PyTorch
```bash
export CMAKE_PREFIX_PATH=${CONDA_PREFIX:-"$(dirname $(which conda))/../"}
python setup.py develop
```

> _Aside:_ If you are using [Anaconda](https://www.anaconda.com/distribution/#download-section), you may experience an error caused by the linker:
>
> ```plaintext
> build/temp.linux-x86_64-3.7/torch/csrc/stub.o: file not recognized: file format not recognized
> collect2: error: ld returned 1 exit status
> error: command 'g++' failed with exit status 1
> ```
>
> This is caused by `ld` from the Conda environment shadowing the system `ld`. You should use a newer version of Python that fixes this issue. The recommended Python version is 3.8.1+.


### Windows

Choose Correct Visual Studio Version.

PyTorch CI uses Visual C++ BuildTools, which come with Visual Studio Enterprise,
Professional, or Community Editions. You can also install the build tools from
https://visualstudio.microsoft.com/visual-cpp-build-tools/. The build tools *do not*
come with Visual Studio Code by default.

If you want to build legacy python code, please refer to [Building on legacy code and CUDA](https://github.com/pytorch/pytorch/blob/main/CONTRIBUTING.md#building-on-legacy-code-and-cuda)


#### CPU-only builds

In this mode PyTorch computations will run on your CPU, not your GPU

```cmd
conda activate
python setup.py develop
```

Note on OpenMP: The desired OpenMP implementation is Intel OpenMP (iomp). In order to link against iomp, you'll need to manually download the library and set up the building environment by tweaking `CMAKE_INCLUDE_PATH` and `LIB`. The instruction [here](https://github.com/pytorch/pytorch/blob/main/docs/source/notes/windows.rst#building-from-source) is an example for setting up both MKL and Intel OpenMP. Without these configurations for CMake, Microsoft Visual C OpenMP runtime (vcomp) will be used.


#### CUDA based build

In this mode PyTorch computations will leverage your GPU via CUDA for faster number crunching

[NVTX](https://docs.nvidia.com/gameworks/content/gameworkslibrary/nvtx/nvidia_tools_extension_library_nvtx.htm) is needed to build Pytorch with CUDA.
NVTX is a part of CUDA distributive, where it is called "Nsight Compute". To install it onto an already installed CUDA run CUDA installation once again and check the corresponding checkbox.
Make sure that CUDA with Nsight Compute is installed after Visual Studio.

Currently, VS 2017 / 2019, and Ninja are supported as the generator of CMake. If `ninja.exe` is detected in `PATH`, then Ninja will be used as the default generator, otherwise, it will use VS 2017 / 2019.
<br/> If Ninja is selected as the generator, the latest MSVC will get selected as the underlying toolchain.

Additional libraries such as
[Magma](https://developer.nvidia.com/magma), [oneDNN, a.k.a. MKLDNN or DNNL](https://github.com/oneapi-src/oneDNN), and [Sccache](https://github.com/mozilla/sccache) are often needed. Please refer to the [installation-helper](https://github.com/pytorch/pytorch/tree/main/.ci/pytorch/win-test-helpers/installation-helpers) to install them.

You can refer to the [build_pytorch.bat](https://github.com/pytorch/pytorch/blob/main/.ci/pytorch/win-test-helpers/build_pytorch.bat) script for some other environment variables configurations



```cmd
cmd

:: Set the environment variables after you have downloaded and unzipped the mkl package,
:: else CMake would throw an error as `Could NOT find OpenMP`.
set CMAKE_INCLUDE_PATH={Your directory}\mkl\include
set LIB={Your directory}\mkl\lib;%LIB%

:: Read the content in the previous section carefully before you proceed.
:: [Optional] If you want to override the underlying toolset used by Ninja and Visual Studio with CUDA, please run the following script block.
:: "Visual Studio 2019 Developer Command Prompt" will be run automatically.
:: Make sure you have CMake >= 3.12 before you do this when you use the Visual Studio generator.
set CMAKE_GENERATOR_TOOLSET_VERSION=14.27
set DISTUTILS_USE_SDK=1
for /f "usebackq tokens=*" %i in (`"%ProgramFiles(x86)%\Microsoft Visual Studio\Installer\vswhere.exe" -version [15^,17^) -products * -latest -property installationPath`) do call "%i\VC\Auxiliary\Build\vcvarsall.bat" x64 -vcvars_ver=%CMAKE_GENERATOR_TOOLSET_VERSION%

:: [Optional] If you want to override the CUDA host compiler
set CUDAHOSTCXX=C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Tools\MSVC\14.27.29110\bin\HostX64\x64\cl.exe

python setup.py develop

```

If you are building for NVIDIA's Jetson platforms (Jetson Nano, TX1, TX2, AGX Xavier), Instructions to install PyTorch for Jetson Nano are [available here](https://devtalk.nvidia.com/default/topic/1049071/jetson-nano/pytorch-for-jetson-nano/)

---

## Build Options

### Build only what you need

`python setup.py build` will build everything by default, but sometimes you are
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
- `USE_ROCM=0` will disable compiling ROCm.
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

---

### Building PyTorch with ASAN

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
