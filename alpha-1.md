It's been a week since pytorch alpha-0.
We're excited to now present alpha-1 :)

##What's new?
We've built a working and unit-tested version of the new nn and autograd packages (torch.nn, torch.autograd) along with a basic draft optim package (torch.optim). The old packages will continue to be available at torch.legacy.*

We've also built fully working serialization (torch.save / torch.load) with features that one expects out of the box like sharing staying intact.

At this point, you can play around with things and get a feel of the new design.

There's an MNIST example at https://github.com/pytorch/examples

A concern raised about pytorch was that Python is a slow language.

It turns out that the MNIST example runs in exactly the same amount of time / epoch in both pytorch and (lua)Torch, and we haven't yet done any optimizations in the code in pytorch yet.
Another notable thing is that pytorch uses 1500MB of system memory vs (lua)Torch's 2300MB. This is before we've added any in-place optimizations into pytorch. The design of the new nn allows us to add seamless memory optimizations without needing the user to mark things as in-place or out-of-place which will bring us more seamless memory savings in pytorch. 

More verbosely:

### torch.nn

We've published an early version of the new nn package.
There are only a few modules right now, but we'll be adding more soon.

There are a couple of advantages over to old package:
* Modules no longer hold temporary buffers and short-lived state. This allows to use the same module a number of times in forward pass, and the gradients will be summed automatically. For example, see how we use the same nn.ReLU object multiple times over here: https://github.com/pytorch/examples/blob/master/mnist/main.py#L43
* There's no longer any need for using rigid container modules. Your model is defined by your code. You can select a completely different path across your model just by adding a number of `if`s. Any crazy branching schemes inside your model are allowed by design.
* It's fully compatible with autograd. Instead of using `nn.Add` or `nn.Index` you can just write this in your model definition: `y = module1(x_1)[0] + module2(x_2)`.
* You can register both forward and backward hooks at each module, which allow you to inspect the intermediate outputs and gradients flowing through the network and the graph.
* [Not Yet Implemented] Safe in-place operations. Tensors used in in-place operations are marked as dirty, and trying to use them in any way raises an error.

### torch.autograd

Autograd at the core of pytorch. Enabling it is just a matter of wrapping your tensors in `Variable` objects before starting the computation (`x = Variable(x)`). Then, when you have your output you can either call `y.backward()` if it's a scalar, or provide gradient w.r.t. the variable as an argument (`y.backward(grad_output)`). Gradients w.r.t. variables are then available in their `.grad` attributes. Please note that only gradients of leaf variables (i.e. created by the user) are computed. If you want to access any gradients of intermediate values, you'll have to use a hook system.

If you don't want to compute gradient for some variables, you can even mark them in a constructor with `requires_grad=False`, and they will be optimized out from the backward pass.

### torch.optim

*Please note that this api is still a bit experimental, and is likely to undergo changes soon.*

optim has a different, more object oriented API. First, you have to create an optimizer object `optimizer = optim.sgd(model, lr=1e-3, momentum=0.9)`. If you don't want to merge the model and criterion in a single object, it's also possible to pass a tuple of `(model, criterion)` as the first argument to a constructor. Then, in your training loop you just call `loss = optimizer.step(input)` (in case of separate model and criterion input should be a tuple of `(input, target)`). This accumulates all the gradients and performs a single optimization step on the parameters.

### Serialization

Tensors supported `pickle` protocol since the beginning of alpha, but pickle can't handle storage/data sharing properly and requires all the data to be copied before serialization. 
We've created `torch.load` and `torch.save`, that have the same interface and solve both of these problems.

### Tensor operators

Thanks to @bart we've added support for `@` operator for matrix multiplication, and changes the `*` to elementwise multiplication.

## Plans for alpha-2:

* Hook system for nn and autograd (for accessing intermediate values)
* More nn modules, autograd options, and optim algorithms
* Inter-process sharing of tensors (for multiprocess data loading or hogwild training)
* New CUDA memory allocator (non-synchronizing CUDA tensors allocations)