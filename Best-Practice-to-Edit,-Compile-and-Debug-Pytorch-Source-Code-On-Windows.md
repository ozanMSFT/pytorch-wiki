[[Page Maintainers | Best Practice to Edit, Compile and Debug PyTorch Source Code On Windows]]:: @mszhanyi

## Goals
In this wiki, I'll introduce the best practice to work with PyTorch C++ Source code on Windows. With these tips, developers could get the better experience with Visual Studio than on Linux.

## Prerequisites:
At first, We need to build the debug version libtorch On Windows. You can refer [building libtorch](https://github.com/pytorch/pytorch/blob/master/docs/libtorch.rst) to build it. 

## Start Visual Studio correctly
Once the libtorch built, please copy the directory of the libiomp5md.dll and add it to the PATH.


