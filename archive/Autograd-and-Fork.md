TLDR: Use `spawn` instead of `fork`.

Autograd engine relies on threads pool, which makes it vulnerable to `fork`. We detect such situations and warn users to use `spawn` method of multiprocessing.

So this code will work 
```python
import multiprocessing as mp

ctx = mp.get_context('spawn')
simple_autograd_function()
with ctx.Pool(3) as pool:
  pool.map(simple_autograd_function, [1, 2, 3])
```

When this code will fail
```python
import multiprocessing as mp

ctx = mp.get_context('fork')
simple_autograd_function()
with ctx.Pool(3) as pool:
  pool.map(simple_autograd_function, [1, 2, 3])
```

See https://docs.python.org/3/library/multiprocessing.html#contexts-and-start-methods for more details.