* Indexing a one-dim Tensor returns a zero-dim Tensor instead of a Python number. For example,

```python
x = torch.Tensor([1, 1, 1])
v = x[0]
x += 1
print(v)  # now variable(2), previously 1
````

* The `type()` of a Tensor no longer reflects the data type. Use `isinstance()` or `x.type()` instead:

```python
x = torch.DoubleTensor([1, 1, 1])
print(type(x)) # now torch.autograd.variable.Variable, previously torch.DoubleTensor
print(x.type())  # OK: 'torch.DoubleTensor'
print(isinstance(x, torch.DoubleTensor))  # OK: True
```

* Reductions (e.g. `sum()`) may overflow on Tensors with small ranges (such as ByteTensor):

```python
>>> x = torch.ByteTensor([100, 100, 100])
>>> x.sum()  # was 300
44
[torch.ByteTensor of size ()]
```