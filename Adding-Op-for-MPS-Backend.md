This page covers references about adding a new Op for the MPS backend.

There are a few options to add support. We usually want to follow these in order:
- If MPS has an existing op for the give PyTorch function, just add a new native function that link the two. An example PR that does thiscan be found [here](https://github.com/pytorch/pytorch/pull/78408).
- Otherwise, for ops that require high performance implementation, we can write custom Metal kernel. There are no example for this yet, feel free to open an issue to discuss this if you're interested.
- As a last resort for ops that are not perf critical, we can make them use the CPU by default. This can be done similarly to first bullet point but the native implementation is replaced by a new entry in [here](https://github.com/pytorch/pytorch/blob/3524428fad4e274fd9145595609effeb775cb51c/aten/src/ATen/mps/MPSFallback.mm#L38).

