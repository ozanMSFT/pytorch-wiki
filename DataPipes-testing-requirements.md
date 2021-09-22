Besides functional testing of every DataPipe, tests should include:

* Test if DataPipe resets correctly (#65067)
```python
 dp = create_dp()
 list1 = list(dp)
 list2 = list(dp)
```
* Test if DataPipe iterators are independent (should not be expected in multiprocessing, but good coding pattern)
* Test if DataPipe is serializable
* Test if DataPipe has `__len__` correctly implemented or throw an expected error
* Test if DataPipe is lazy (serialized size is 'reasonable')
* Test how DataPipe works in deterministic context
* Test if DataPipes creates properly linked DataPipes graph
* Test if DataPipe is picklable (using pickle or dill)


Ideally, there are examples and helper functions for each of these requirements.