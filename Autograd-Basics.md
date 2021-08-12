[[Page Maintainers|How to add a new page, edit a page, or suggest edits]]: @alband, @soulitzer

## Scope
* Understand how backpropagation works in theory
* Understand how to derive backward formulas and how to add a backward formula to an operator
* Understand what a composite autograd operators is and when it is useful
* Know when to use gradcheck and custom autograd Functions
* (optional) Understand how the autograd graph gets built and executed

## Introduction to backpropagation

Read through [link](https://colab.research.google.com/drive/1aWNdmYt7RcHMbUk-Xz2Cv5-cGFSWPXe0).

## What should I do when I implement a new function?

The answer varies a lot depending on the properties of the function you're writing.
You can use the table below to see what is the best solution for you.
The next sections in this document give more information on each solution.
All terms are defined below the table.

| Where \ Level      | Tensor | Third party lib | Raw data pointer access |
| ----------- | ----------- | ----------- | ----------- |
| Aten native functions      | Composite | derivatives.yaml | derivatives.yaml
| In pytorch/pytorch outside of aten   | Composite | Custom Function | Custom Function |
| Outside of pytorch/pytorch   | Composite | Custom Function | Custom Function |

Definition of the Levels:
- "Tensor" means that your function is always working with PyTorch Tensors and using differentiable ops.
- "Third party lib" means that your function uses a third party lib that doesn't work with Tensors directly (numpy, CUDA, etc).
- "Raw data pointer access" means that your function extracts the data_ptr from the Tensor and work with that directly.

Definition of the Wheres:
- "Aten native function" means functions that are defined in `native_functions.yaml`.
- "In pytorch/pytorch outside of aten" means functions that are in pytorch/pytorch but not in aten (both in python and c++).
- "Outside of pytorch/pytorch" means functions implemented outside of pytorch/pytorch.

Defintion of solutions:
- "Composite" means that this new function is not an elementary op from the point of view of autograd. And autograd will track all the other functions you're using inside.
- "derivatives.yaml" means that you should implement your derivatives using `tools/autograd/derivatives.yaml`.
- "Custom Function" means that you should use custom autograd Function to wrap your function.

## Given an operator, how do I derive a backward formula for it?

- How to derive a simple formula: torch.sin [link](https://colab.research.google.com/drive/1lUU5JUh0h-8XwaavyLuOkQfeQgn4m8zr).
- How to derive a more advanced formula: torch.mm [link](https://colab.research.google.com/drive/1z6641HKB51OfYJMCxOFo0lYd7viytnIG).

## Given a new operator, how do I write a new backward formula? (using derivatives.yaml)

Coming soon!

## How do I test an autograd formula?

Coming soon!

## What are custom autograd functions?

Coming soon!

## Try out the Autograd Onboarding Lab

https://github.com/pytorch/pytorch/wiki/Autograd-Onboarding-Lab


