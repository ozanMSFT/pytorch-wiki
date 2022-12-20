Page Maintainers: @zou3519

## Scope
* understand what composable function transforms are and their most common use cases
* understand what DynamicLayerStack is and how it is used to implement composition of function transforms

## Learn about function transforms
* Read through [the whirlwind tour](https://pytorch.org/functorch/stable/notebooks/whirlwind_tour.html)
* Read through the [advanced autodiff tutorial](https://pytorch.org/functorch/stable/notebooks/jacobians_hessians.html)
* Read through the [per-sample-gradients tutorial](https://pytorch.org/functorch/stable/notebooks/per_sample_grads.html)
* Read through the [model ensembling tutorial](https://pytorch.org/functorch/stable/notebooks/ensembling.html)

## Exercise

The advanced autodiff tutorial explains how to compute Jacobians via a composition of vmap and vjp.
1. Without looking at the source code for jacfwd or torch.autograd.functional.jacobian, write a function to compute the Jacobian using forward-mode AD and a for-loop. Note that forward-mode AD computes Jacobian-vector products while reverse-mode AD (vjp, grad) compute vector-Jacobian products.
2. Write a function to compute the Jacobian by composing vmap and jvp.

The APIs should have the following signature:
```
def jacobian(f, *args):
    pass
```
You can assume that `f` accepts multiple Tensor arguments and returns a single Tensor argument.

## Understand how PyTorch implements composable function transforms

TODO(rzou)