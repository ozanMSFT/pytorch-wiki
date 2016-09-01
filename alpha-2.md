##What's new?

We've 

* built seamless support for multiprocessing with Tensor sharing
* changed the API of the optim engine
* added a complete Hook system for nn and autograd
* added in-place ops to autograd and more neural network modules to nn

## Multiprocessing with Tensor sharing

In Torch, or in general, one uses "threads" to build parallel data loaders, as well as to do Hogwild training.  
Threads are powerful, as one can share Tensors between threads.  
This allows you to:
* transfer data between threads with efficiently with zero memory copy and serialization overhead.
* share tensors among threads for parameter sharing models

Sharing Tensors among threads is very useful when you do Hogwild training, i.e. if you want to train several models in parallel, but want to share their underlying parameters.  
This is often used in non ConvNets, like training word embeddings, RL-for-games, etc.

With Python, one cannot use threads because of a few technical issues.  
Python has what is called [Global Interpreter Lock](https://wiki.python.org/moin/GlobalInterpreterLock), which does not allow threads to concurrently execute python code.

Hence, the most pythonic way to use multiple CPU cores is [multiprocessing](http://docs.python.org/2/library/multiprocessing.html)

We made PyTorch to seamlessly integrate with python multiprocessing.  
This involved solving some complex technical problems to make this an air-tight solution, and more can be read [in this in-depth technical discussion](http://github.com/pytorch/pytorch/wiki/Multiprocessing-Technical-Notes).

What this means for you as the end-user is that you can simply use multiprocessing in this way:

```python
# loaders.py
# Functions from this file run in the workers

def fill(queue):
  while True:
    tensor = queue.get()
    tensor.fill_(10)
    queue.put(tensor)

def fill_pool(tensor):
  tensor.fill_(10)
```

```python
# Example 1: Using multiple persistent processes and a Queue
# process.py

import torch
import torch.multiprocessing as multiprocessing
from loaders import fill

# torch.multiprocessing.Queue automatically moves Tensor data to shared memory
# So the main process and worker share the data
queue = multiprocessing.Queue()
buffers = [torch.Tensor(2, 2) for i in range(4)]
for b in buffers:
  queue.put(b)
processes = [multiprocessing.Process(target=fill, args=(queue,)).start() for i in range(10)]
```

```python
# Example 2: Using a process pool
# pool.py

import torch
from torch.multiprocessing import Pool
from loaders import fill_pool

tensors = [torch.Tensor(2, 2) for i in range(100)]
pool = Pool(10)
pool.map(fill_pool, tensors)
```


##Optim's API changes

Optimizer's step function now accepts a closure that should return a loss variable (similar to `legacy.optim`).

We've realized that to keep Optim flexible for multiple methods, like SGD with nesterov, Conjugate Gradient, LBFGS etc., we need to have the input to optim be a function that evaluates the model. 
This is necessary because several optimization methods re-evaluate the function multiple times at different parameters.
To come to this necessary API change, we took into account complicated scenarios like Dynamic RNNs and complex ConvNet models with dynamic branching.  

So the API now looks like this:

```python
optimizer = optim.SGD(model, lr=1e-3, momentum)
input, target = ...
optimizer.step(lambda: criterion(model(input), target)) #sufficient for simple models
```

To simplify things at the user end for simple or specific common models, we will introduce a Trainer class, that will take a (dataset, model, optim) triple and train the model. This trainer class is planned for alpha-3.

##A complete Hook system for nn and autograd

Accessing intermediate values during the forward pass is straightforward, but during backward the buffers can rapidly change their content (for example: when doing in-place optimizations). 

If you want to get access to the gradients at a particular Op or Layer inside your model, one uses a hook system. 
Hooks can be attached to variables or to modules and are called as soon as the gradient is available:

```python
# Example in autograd
a, b, c = [Variable(torch.Tensor(5, 5)) for i in range(3)]

def print_norm(grad):
    print(grad.norm(2))

y = b * c + a
y.register_hook(print_norm)

z = y * y - b
z.backward(torch.ones(5, 5))

# Example in nn
model = ...

def inspect_forward(module, input, output):
    ...

model.conv2.register_forward_hook(inspect_forward)

def inspect_backward(module, grad_input, grad_output):
    ...
    
model.conv2.register_backward_hook(inspect_backward)
```

We would definitely look forward to comments about the Hook system. Let us know what you think.

### Added in-place ops to autograd and more neural network modules to nn
* As part of porting fb.resnet.torch, we've added AveragePool2d and fixed BatchNorm2d
* Now, autograd fully supports in-place operations, with in-place variables immediately marked as dirty.
To illustrate this, let's look at a small example

```python
x = Variable(torch.ones(5, 5))
y = Variable(torch.ones(5, 5) * 4)

z = x * y
q = z * y
r = z + y
z.add_(y)
# z is a the last expression, so this should succeed
z.backward(torch.ones(5, 5))

# r doesn't use the z in it's backward, so it should succeed
r.backward(torch.ones(5, 5))

# however, q needs z in it's backward, but z has now been 
# marked as dirty (because it was used in an in-place operation)
# this line will hence raise an error
q.backward(torch.ones(5, 5))
```


##Plans for alpha-3

* Unit tests for multiprocessing
* Add more nn modules and autograd functions ( we're porting fb.resnet.torch )
* New CUDA memory allocator (non-synchronizing CUDA tensors allocations)
    * We've made progress on this, but it is not complete yet
* Trainer and Dataset classes
* Continuous builds for CUDA (using Nimbix)
* Binary packages (nightly and versioned)

