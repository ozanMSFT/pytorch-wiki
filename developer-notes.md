raw notes
=========

#### Where / how is Tensor exposed in python?


- https://github.com/pytorch/pytorch/blob/master/torch/csrc/generic/Tensor.h#L5
- init definition: https://github.com/pytorch/pytorch/blob/master/torch/csrc/generic/Tensor.cpp#L554-L600
- initialized here: https://github.com/pytorch/pytorch/blob/master/torch/csrc/generic/Tensor.cpp#L608-L610
- constructor: pynew: https://github.com/pytorch/pytorch/blob/master/torch/csrc/generic/Tensor.cpp#L84-L293
- destructor: https://github.com/pytorch/pytorch/blob/master/torch/csrc/generic/Tensor.cpp#L53-L57


