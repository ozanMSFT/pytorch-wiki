## Scope

* Understand what an operator is in PyTorch
* Understand what a Tensor is (including stride, dtype, and layout)
* Understand what views are
* Understand how to author an operator in PyTorch
* Understand how to test operators in PyTorch
* Understand what TensorIterator is


## What is a Tensor?

A Tensor [consists of](https://github.com/pytorch/pytorch/blob/2d6fd22e241763da67e8076a8d3443d988215f32/c10/core/TensorImpl.cpp#L84-L90):

* data_ptr, a pointer to a chunk of memory
* some *sizes* metadata
* some *strides* metadata
* a storage offset

## How to author an operator

[Comprehensive guide](https://github.com/pytorch/pytorch/blob/master/aten/src/ATen/native/README.md)

## TensorIterator

- Read through the colab notebook ([link](https://colab.research.google.com/drive/1vOiQjcp6eESOChCxDQEPQXw3c0yKVjbP))
- Listen to the podcast ([link](https://podcasts.apple.com/us/podcast/tensoriterator/id1566080008?i=1000523781021))

## Learn about view operations

- Read through the colab notebook ([link](https://colab.research.google.com/drive/1rJP2aw-f5Iwqwri0_Ei_OI8_esh1Nyeq?usp=sharing))
- Try out the Tensor Views lab ([link](https://colab.research.google.com/drive/1rJP2aw-f5Iwqwri0_Ei_OI8_esh1Nyeq#scrollTo=LpTEuP0JZzav&line=1&uniqifier=1))




