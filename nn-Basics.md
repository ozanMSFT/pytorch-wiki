## Scope
* Understand what `torch.nn` is
* Understand what a Module is
* Understand how Modules are used to build and train neural networks
* Understand how to author a Module in PyTorch
* Understand how to test Modules in PyTorch

## What is torch.nn?
`torch.nn` is the component of PyTorch that provides building blocks for neural networks. Its core abstraction is `nn.Module`, which encapsulates stateful computation with learnable parameters. Modules integrate with the [[autograd|Autograd-Basics]] system and are generally trained using optimizers provided in [`torch.optim`](https://pytorch.org/docs/stable/optim.html).

## What is a Module?
Read through the following links:
* [Introduction to Modules](https://pytorch.org/docs/stable/notes/modules.html)
* [Neural networks tutorial](https://pytorch.org/tutorials/beginner/blitz/neural_networks_tutorial.html)

## How to author and test a Module in PyTorch
Work through the [[lab|Module-Onboarding-Lab]].