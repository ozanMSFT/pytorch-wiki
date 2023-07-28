This wiki lists the steps for setting up a developer environment to contribute code changes to PyTorch.

## Prerequisites

<!-- Source: https://github.com/pytorch/pytorch#prerequisites -->

To develop PyTorch you will need:
- Python 3.8 or later (for Linux, Python 3.8.1+ is needed)
- A C++17 compatible compiler, such as clang

### (optional) CUDA Support
If you want to compile with CUDA support, install the following (note that CUDA is not supported on macOS)
- [NVIDIA CUDA](https://developer.nvidia.com/cuda-downloads) 11.0 or above
- [NVIDIA cuDNN](https://developer.nvidia.com/cudnn) v8 or above
- [Compiler](https://gist.github.com/ax3l/9489132) compatible with CUDA

Note: You could refer to the [cuDNN Support Matrix](https://docs.nvidia.com/deeplearning/cudnn/pdf/cuDNN-Support-Matrix.pdf) for cuDNN versions with the various supported CUDA, CUDA driver and NVIDIA hardware

### (optional) ROCm Support
If you want to compile with ROCm support, install
- [AMD ROCm](https://rocmdocs.amd.com/en/latest/Installation_Guide/Installation-Guide.html) 4.0 and above installation
- ROCm is currently supported only for Linux systems.



----

## Next Steps
* [[Fork, clone, and checkout the PyTorch source|Fork Clone and Checkout]]
