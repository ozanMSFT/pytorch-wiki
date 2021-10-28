## Development Setup

Please see https://github.com/pytorch/pytorch/blob/master/CONTRIBUTING.md#developing-pytorch

For fb employees, please read through [this](https://www.internalfb.com/intern/wiki/PyTorch/PyTorchDev/Workflow/PyTorch_environment_setup/) as well.

## Learn about how to use PyTorch

Run through the [PyTorch 60 Minute Blitz](https://pytorch.org/tutorials/beginner/deep_learning_60min_blitz.html)

If you're not familiar with machine learning, [here](https://www.youtube.com/playlist?list=PLC1qU-LWwrF64f4QKQT-Vg5Wr4qEE1Zxk) is a nice lecture series.

## Understand how PyTorch is used in the open-source community

* [Watch a high-level overview of PyTorch by Joe Spisak](https://youtu.be/5h1Ot2dPi2E?t=1393)
* [Take a look at PyTorch tutorials](https://pytorch.org/tutorials/)
* [Take a look at PyTorch examples](https://github.com/pytorch/examples)

Explore popular open-source models and frameworks using PyTorch
   * [Detectron and Mask R-CNN, Computer Vision](https://github.com/facebookresearch/maskrcnn-benchmark)
   * [PyText, NLP](https://github.com/facebookresearch/pytext)
   * [Horizon, Applied Reinforcement Learning](https://github.com/facebookresearch/Horizon)

## Workflow tips and tricks

### Build only what you need

If you don't need CUDA, build using `USE_CUDA=0`: the build is significantly faster. There are also a lot of other build flags that help get rid of components that you might not work on. Below is an opinionated build command that gets rid of a lot of different options that don't get used very often.
```
USE_KINETO=0 BUILD_CAFFE2=0 USE_DISTRIBUTED=0 USE_NCCL=0 BUILD_TEST=0 USE_XNNPACK=0 USE_FBGEMM=0 USE_QNNPACK=0 USE_MKLDNN=0 USE_MIOPEN=0 USE_NNPACK=0 BUILD_CAFFE2_OPS=0 USE_TENSORPIPE=0 python setup.py develop
```

(See [this](https://github.com/pytorch/pytorch/blob/master/CONTRIBUTING.md#build-only-what-you-need) for more details)


### Use viable/strict

The head of the pytorch/pytorch master branch may have test failures ([see here for the current state](https://hud.pytorch.org/build2/pytorch-master)). When developing PyTorch, instead of branching off of `master`, you can branch off of `viable/strict`. `viable/strict` is a branch that lags behind master and guarantees that all PyTorch tests are passing on the branch. Basing your work off of `viable/strict` gives you confidence that any test failures are actually your code's fault.

Some quick git tips:
```
# Creating a new feature branch off of viable/strict
git checkout viable/strict
git checkout -b my_new_feature

# Rebasing your work to appear on top of viable/strict, assuming upstream points to pytorch/pytorch.
# (Some people develop with origin pointing to pytorch/pytorch)
git pull --rebase upstream viable/strict
```
### For more details

See this detailed section in our [CONTRIBUTING.MD](https://github.com/pytorch/pytorch/blob/master/CONTRIBUTING.md#c-development-tips)

## Submitting a change to PyTorch

Please see https://github.com/pytorch/pytorch/blob/master/CONTRIBUTING.md

For fb employees, please see [here](https://fb.quip.com/Qym6ATnVdb5O)