Pointers from high-level code to low-level C

for those who are interested. here are some pointers regarding the implementation of the loss functions. A similar thing can be done for the other modules.
* [raw C implementation](https://github.com/pytorch/pytorch/blob/master/torch/lib/THNN/generic/MSECriterion.c)
* [automatic wrapping of the C code in python](https://github.com/pytorch/pytorch/blob/master/torch/nn/_functions/thnn/auto.py#L237)
* [base class that automatically figures out the name of the function to be called via the name of the class](https://github.com/pytorch/pytorch/blob/master/torch/nn/modules/loss.py#L23-L24)
* [MSE implementation that is exposed to the end user](https://github.com/pytorch/pytorch/blob/master/torch/nn/modules/loss.py#L166)