* Indexing a one-dim Tensor returns a zero-dim Tensor instead of a Python number. For example,

```python
x = torch.Tensor([1, 1, 1])
v = x[0]
x += 1
print(v)  # now variable(2), previously 1
````
