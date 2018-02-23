The Tensor and Variable classes had slightly different semantics. There are some breaking changes from combining the two classes.

* Indexing a one-dim Tensor returns a zero-dim Tensor instead of a Python number. The zero-dim Tensor shares the storage of the indexed Tensor. For example:

```python
>>> x = torch.Tensor([1, 1, 1])
>>> v = x[0]
>>> x += 1
>>> print(v)  # was 1
 2
[torch.FloatTensor of size ()]
````

* The `type()` of a Tensor no longer reflects the data type. Use `isinstance()` or `x.type()` instead:

```python
>>> x = torch.DoubleTensor([1, 1, 1])
>>> print(type(x)) # was torch.DoubleTensor
<class 'torch.autograd.variable.Variable'>
>>> print(x.type())  # OK: 'torch.DoubleTensor'
'torch.DoubleTensor'
>>> print(isinstance(x, torch.DoubleTensor))  # OK: True
True
```

* Reductions (e.g. `sum()`) may overflow on Tensors with small ranges (such as ByteTensor):

```python
>>> x = torch.ByteTensor([100, 100, 100])
>>> x.sum()  # was 300
44
[torch.ByteTensor of size ()]
```

* Tensor.copy_() always obeys broadcasting rules. Tensor assignment (`x[idx] = value`) also follows broadcasting rules more closely:

```python
>>> x = torch.randn(3, 3)
>>> y = torch.randn(9)
>>> x.copy_(y, broadcast=False) # ERROR
>>> x.copy_(y.view_as(x))  # OK
```

Minor:
* `masked_scatter(...)` and `masked_fill(...)` follow in place broadcasting rules.
* `matmul` no longer has an `out` parameter.