## Prerequisites

Source: https://github.com/pytorch/pytorch#prerequisites

<!-- If you are installing from source, you will need: -->
To develop PyTorch you will need:
- Python 3.8 or later (for Linux, Python 3.8.1+ is needed)
- A C++17 compatible compiler, such as clang

We highly recommend installing an [Anaconda](https://www.anaconda.com/distribution/#download-section) environment. You will get a high-quality BLAS library (MKL) and you get controlled dependency versions regardless of your Linux distro.

### CUDA Support
If you want to compile with CUDA support, install the following (note that CUDA is not supported on macOS)
- [NVIDIA CUDA](https://developer.nvidia.com/cuda-downloads) 11.0 or above
- [NVIDIA cuDNN](https://developer.nvidia.com/cudnn) v7 or above
- [Compiler](https://gist.github.com/ax3l/9489132) compatible with CUDA

Note: You could refer to the [cuDNN Support Matrix](https://docs.nvidia.com/deeplearning/cudnn/pdf/cuDNN-Support-Matrix.pdf) for cuDNN versions with the various supported CUDA, CUDA driver and NVIDIA hardware

### ROCm Support
If you want to compile with ROCm support, install
- [AMD ROCm](https://rocmdocs.amd.com/en/latest/Installation_Guide/Installation-Guide.html) 4.0 and above installation
- ROCm is currently supported only for Linux systems.

---

## Install Dependencies

**Common**

```bash
conda install cmake ninja
# Run this command from the PyTorch directory after cloning the source code using the “Get the PyTorch Source“ section below
pip install -r requirements.txt
```

**On Linux**

```bash
conda install mkl mkl-include
# CUDA only: Add LAPACK support for the GPU if needed
conda install -c pytorch magma-cuda110  # or the magma-cuda* that matches your CUDA version from https://anaconda.org/pytorch/repo

# (optional) If using torch.compile with inductor/triton, install the matching version of triton
# Run from the pytorch directory after cloning
make triton
```

**On MacOS**

```bash
# Add this package on intel x86 processor machines only
conda install mkl mkl-include
# Add these packages if torch.distributed is needed
conda install pkg-config libuv
```

**On Windows**

```bash
conda install mkl mkl-include
# Add these packages if torch.distributed is needed.
# Distributed package support on Windows is a prototype feature and is subject to changes.
conda install -c conda-forge libuv=1.39
```
