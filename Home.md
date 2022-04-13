Welcome to the PyTorch developer's wiki!

*Please read our [[best practices|Where or how should I add documentation]] if you're interested in adding a page or making edits*

## User docs

* [Release notes](https://github.com/pytorch/pytorch/releases)
* [PyTorch Versions](PyTorch-Versions)
* [Public API definition and documentation](Public-API-definition-and-documentation)

## Onboarding

New to PyTorch? Don't know where to start?
- [[Core Onboarding|Core Frontend Onboarding]]

## Developer docs

* [Developer FAQ](Developer-FAQ)
* [[Where should I add documentation?|Where or how should I add documentation]]
* [PyTorch Data Flow and Interface Diagram](PyTorch-Data-Flow-and-Interface-Diagram)
* [Multiprocessing Technical Notes](Multiprocessing-Technical-Notes)
* [Software Architecture for c10](Software-Architecture-for-c10)
* [PyTorch JIT IR format](PyTorch-IR) (slightly out of date now)
* [TH to ATen porting guide](TH-to-ATen-porting-guide)
* [Writing Python in C++ (a manifesto)](Writing-Python-in-cpp-(a-manifesto))
* [Introducing Quantized Tensor](Introducing-Quantized-Tensor)
* [Life of a Tensor](Life-of-A-Tensor)
* [How to use `TensorIterator`](How-to-use-TensorIterator)
* [Running and writing tests](Running-and-writing-tests)
* [Writing memory format aware operators](Writing-memory-format-aware-operators)
* [Guide for adding type annotations to PyTorch](Guide-for-adding-type-annotations-to-PyTorch)
* [The torch.fft module in PyTorch 1.7](The-torch.fft-module-in-PyTorch-1.7)
* [PyTorch-ONNX exporter](PyTorch-ONNX-exporter)

### Notes
* [Automatic Mixed Precision package](https://github.com/pytorch/pytorch/blob/master/docs/source/amp.rst)
* [Automatic Mixed Precision examples](https://github.com/pytorch/pytorch/blob/master/docs/source/notes/amp_examples.rst)
* [Autograd mechanics](https://github.com/pytorch/pytorch/blob/master/docs/source/notes/autograd.rst)
* [Broadcasting semantics](https://github.com/pytorch/pytorch/blob/master/docs/source/notes/broadcasting.rst)
* [CPU threading and TorchScript inference](https://github.com/pytorch/pytorch/blob/master/docs/source/notes/cpu_threading_torchscript_inference.rst)
* [CUDA semantics](https://github.com/pytorch/pytorch/blob/master/docs/source/notes/cuda.rst)
* [Frequently Asked Questions](https://github.com/pytorch/pytorch/blob/master/docs/source/notes/faq.rst)
* [Extending PyTorch](https://github.com/pytorch/pytorch/blob/master/docs/source/notes/extending.rst)
* [Features for large-scale deployments](https://github.com/pytorch/pytorch/blob/master/docs/source/notes/large_scale_deployments.rst)
* [Multiprocessing best practices](https://github.com/pytorch/pytorch/blob/master/docs/source/notes/multiprocessing.rst)
* [Reproducibility](https://github.com/pytorch/pytorch/blob/master/docs/source/notes/randomness.rst)
* [Serialization semantics](https://github.com/pytorch/pytorch/blob/master/docs/source/notes/serialization.rst)
* [Windows FAQ](https://github.com/pytorch/pytorch/blob/master/docs/source/notes/windows.rst)
* [Python Language Reference Coverage](https://github.com/pytorch/pytorch/blob/master/docs/source/jit_python_reference.rst)
* [Complex Numbers](https://github.com/pytorch/pytorch/blob/master/docs/source/complex_numbers.rst)
* [Android](https://github.com/pytorch/pytorch/blob/master/android/README.md)
* [iOS](https://github.com/pytorch/pytorch/blob/master/ios/README.md)
* [How-to: Writing PyTorch & Caffe2 Operators](https://github.com/pytorch/pytorch/blob/master/aten/src/ATen/core/op_registration/README.md)
* [CUDA IPC Refcounting implementation explained](https://github.com/pytorch/pytorch/blob/master/torch/multiprocessing/cuda_multiprocessing.md)
* [Autograd](https://github.com/pytorch/pytorch/blob/master/torch/csrc/autograd/README.md)
* [Code Coverage Tool for Pytorch](https://github.com/pytorch/pytorch/blob/master/tools/code_coverage/README.md)
* [How to write tests using FileCheck](https://github.com/pytorch/pytorch/blob/master/test/HowToWriteTestsUsingFileCheck.md)
* [PyTorch Release Scripts](https://github.com/pytorch/pytorch/blob/master/scripts/release/README.md)
* [Serialized operator test framework](https://github.com/pytorch/pytorch/blob/master/caffe2/python/serialized_test/README.md)
* [Observers](https://github.com/pytorch/pytorch/blob/master/caffe2/observers/README.md)
* [Snapdragon NPE Support](https://github.com/pytorch/pytorch/blob/master/caffe2/mobile/contrib/snpe/README.md)
* [Using TensorBoard in ifbpy](https://github.com/pytorch/pytorch/blob/master/caffe2/contrib/tensorboard/tensorboard.md)

### Named Tensors
* [Named Tensors](https://github.com/pytorch/pytorch/blob/master/docs/source/named_tensor.rst)
* [Named Tensors operator coverage](https://github.com/pytorch/pytorch/blob/master/docs/source/name_inference.rst)

### Quantization
* [Introduction to Quantization](https://github.com/pytorch/pytorch/blob/master/docs/source/quantization.rst)
* [Quantization Operation coverage](https://github.com/pytorch/pytorch/blob/master/docs/source/quantization-support.rst)
* [Implementing native quantized ops](https://github.com/pytorch/pytorch/blob/master/aten/src/ATen/native/quantized/README.md)
* [Extend PyTorch Quantization to Custom Backends](https://github.com/pytorch/rfcs/blob/master/RFC-0019-Extending-PyTorch-Quantization-to-Custom-Backends.md)

### JIT/TorchScript 
* [JIT Technical Overview](https://github.com/pytorch/pytorch/blob/master/torch/csrc/jit/OVERVIEW.md)
* [Current workflow](https://github.com/pytorch/pytorch/blob/master/torch/csrc/jit/tensorexpr/DesignOverview.md)
* [Static Runtime](https://github.com/pytorch/pytorch/blob/master/torch/csrc/jit/runtime/static/README.md)
* [TorchScript serialization](https://github.com/pytorch/pytorch/blob/master/torch/csrc/jit/docs/serialization.md) 
* [PyTorch Fuser](https://github.com/pytorch/pytorch/blob/master/torch/csrc/jit/codegen/fuser/README.md)
* [Implementation reference for the CUDA PyTorch JIT Fuser](https://github.com/pytorch/pytorch/blob/master/torch/csrc/jit/codegen/cuda/docs/main_page.md)
* [TorchScript](https://github.com/pytorch/pytorch/blob/master/docs/source/jit.rst)
* [TorchScript Language Reference](https://github.com/pytorch/pytorch/blob/master/docs/source/jit_language_reference.rst)
* [TorchScript Unsupported Pytorch Constructs](https://github.com/pytorch/pytorch/blob/master/docs/source/jit_unsupported.rst)

### Distributed 
* [Distributed RPC Framework](https://github.com/pytorch/pytorch/blob/master/docs/source/rpc.rst)
* [Distributed Autograd Design](https://github.com/pytorch/pytorch/blob/master/docs/source/rpc/distributed_autograd.rst)
* [Remote Reference Protocol](https://github.com/pytorch/pytorch/blob/master/docs/source/rpc/rref.rst)
* [Distributed Data Parallel](https://github.com/pytorch/pytorch/blob/master/docs/source/notes/ddp.rst)
* [Distributed communication package](https://github.com/pytorch/pytorch/blob/master/docs/source/distributed.rst)
* [Contributing to PyTorch Distributed](https://github.com/pytorch/pytorch/blob/master/torch/distributed/CONTRIBUTING.md)

### C++
* [PyTorch with C++](https://github.com/pytorch/pytorch/blob/master/docs/source/cpp_index.rst)
* [The C++ Frontend](https://github.com/pytorch/pytorch/blob/master/docs/cpp/source/frontend.rst)
* [PyTorch C++ API](https://github.com/pytorch/pytorch/blob/master/docs/cpp/source/index.rst)
* [Tensor basics](https://github.com/pytorch/pytorch/blob/master/docs/cpp/source/notes/tensor_basics.rst)
* [Tensor Creation API](https://github.com/pytorch/pytorch/blob/master/docs/cpp/source/notes/tensor_creation.rst)
* [Tensor Indexing API](https://github.com/pytorch/pytorch/blob/master/docs/cpp/source/notes/tensor_indexing.rst)
* [MaybeOwned\<Tensor\>](https://github.com/pytorch/pytorch/blob/master/docs/cpp/source/notes/maybe_owned.rst)
* [Installing C++ Distributions of PyTorch](https://github.com/pytorch/pytorch/blob/master/docs/cpp/source/installing.rst)
* [Torch Library API](https://github.com/pytorch/pytorch/blob/master/docs/cpp/source/library.rst)
* [libtorch](https://github.com/pytorch/pytorch/blob/master/docs/libtorch.rst)
* [C++ / Python API parity tracker](https://github.com/pytorch/pytorch/blob/master/test/cpp_api_parity/parity-tracker.md)
* [TensorExpr C++ Tests](https://github.com/pytorch/pytorch/blob/master/test/cpp/tensorexpr/README.md)
* [JIT C++ Tests](https://github.com/pytorch/pytorch/blob/master/test/cpp/jit/README.md)
* [C++ Frontend Tests](https://github.com/pytorch/pytorch/blob/master/test/cpp/api/README.md)
* [FAQ](https://github.com/pytorch/pytorch/blob/master/docs/cpp/source/notes/faq.rst)
* [Best Practices to Edit and Compile Pytorch Source Code On Window](Best-Practices-to-Edit-and-Compile-Pytorch-Source-Code-On-Windows)

### Benchmarks
* [Distributed Data Parallel Benchmark](https://github.com/pytorch/pytorch/blob/master/benchmarks/distributed/ddp/README.md)
* [Fast RNN benchmarks](https://github.com/pytorch/pytorch/blob/master/benchmarks/fastrnns/README.md)
* [PyTorch/Caffe2 Operator Micro-benchmarks](https://github.com/pytorch/pytorch/blob/master/benchmarks/operator_benchmark/README.md)
* [__torch_function__ micro-benchmarks](https://github.com/pytorch/pytorch/blob/master/benchmarks/overrides_benchmark/README.md)
* [Benchmarking tool for the autograd AP](https://github.com/pytorch/pytorch/blob/master/benchmarks/functional_autograd_benchmark/README.md)
* [Modular Benchmarking Components](https://github.com/pytorch/pytorch/blob/master/torch/utils/benchmark/README.md)

### DataLoader
* [DataPipe](https://github.com/pytorch/data#what-are-datapipes)
* [DataPipe test requirements](https://github.com/pytorch/pytorch/wiki/DataPipes-testing-requirements)

## Workflow docs

* [Continuous Integration](Continuous-Integration)
* [Bot commands](Bot-commands)
* [Code review values](Code-review-values)
* [Lint as you type](Lint-as-you-type)
* [Pull request review etiquette](Pull-request-review-etiquette)
* [Debugging with SSH on Github Actions](Debugging-using-with-ssh-for-Github-Actions)
* [Using hud.pytorch.org](Using-hud.pytorch.org)

## Community
* [Code of Conduct](https://github.com/pytorch/pytorch/blob/master/CODE_OF_CONDUCT.md)
* [Contributing](https://github.com/pytorch/pytorch/blob/master/CONTRIBUTING.md)
* [PyTorch Contribution Guide](https://github.com/pytorch/pytorch/blob/master/docs/source/community/contribution_guide.rst)
* [PyTorch Governance](https://github.com/pytorch/pytorch/blob/master/docs/source/community/governance.rst)

## Archived

* [Breaking Changes from Variable and Tensor merge](Breaking-Changes-from-Variable-and-Tensor-merge) (from 0.4 release)
* [Tensor API changes for Caffe2 developers](Tensor-API-changes-for-Caffe2-developers) (from 1.0 release, plus some stuff on master)
* [Autograd and Fork](Autograd-and-Fork)

#### Caffe2
* [Caffe2](https://github.com/pytorch/pytorch/blob/master/caffe2/README.md)
* [Building Caffe2](https://github.com/pytorch/pytorch/blob/master/docs/caffe2/installation.md)
* [Doxygen Notes](https://github.com/pytorch/pytorch/blob/master/docs/caffe2/DOXYGEN.md)
* [Docker & Caffe2](https://github.com/pytorch/pytorch/blob/master/docker/caffe2/readme.md)
* [Caffe2 implementation of Open Neural Network Exchange (ONNX)](https://github.com/pytorch/pytorch/blob/master/caffe2/python/onnx/README.md)
* [Caffe2 ONNX op coverage](https://github.com/pytorch/pytorch/blob/master/caffe2/python/onnx/ONNXOpCoverage.md)
* [nomnigraph](https://github.com/pytorch/pytorch/blob/master/caffe2/core/nomnigraph/README.md)
* [Caffe2 & TensorRT integration](https://github.com/pytorch/pytorch/blob/master/caffe2/contrib/tensorrt/README.md)
* [Playground for Caffe2 Models](https://github.com/pytorch/pytorch/blob/master/caffe2/contrib/playground/README.md)
* [How to run FakeLowP vs Glow tests](https://github.com/pytorch/pytorch/blob/master/caffe2/contrib/fakelowp/test/README.md)
* [Using ONNX and ATen to export models from PyTorch to Caffe2](https://github.com/pytorch/pytorch/blob/master/caffe2/contrib/aten/docs/pytorch_to_caffe2.md)
* [An ATen operator for Caffe2](https://github.com/pytorch/pytorch/blob/master/caffe2/contrib/aten/README.md)
