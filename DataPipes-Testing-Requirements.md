Besides functional testing of every DataPipe, tests should include:

* Test if DataPipe resets correctly (#65067)
  * Order of the outputs should be consistent between iterations
```python
 dp = create_dp()
 list1 = list(dp)
 list2 = list(dp)
```
* Test if DataPipe iterators are independent (should not be expected in multiprocessing, but good coding pattern)
* Test if DataPipe is [serializable](https://github.com/pytorch/data/issues/172)
* Test if DataPipe has `__len__` correctly implemented or throws an expected error
* Test if DataPipe is lazy (serialized size is 'reasonable')
* Test how DataPipe works in deterministic context
* Test if DataPipes creates properly linked DataPipes graph
* Test if DataPipe is picklable (using pickle or dill)
  * Test if it is still picklable after the DataPipe has been iterated through.

Ideally, there are examples and helper functions for each of these requirements.

TODO: Some of these requirements are only relevant for `IterDataPipe`, we also need to requirements for `MapDataPipe`.