Decorator `@torchtest.test_all_device_types()` allows to run test multiple times with all available device types.

For example:

```python
class _TestTorchMixin(torchtest):

    # .....
     
    @torchtest.test_all_device_types()
    def test_zeros_like(self, device):
    '''
       device is str type 'cpu', 'cuda'
    '''
        expected = torch.zeros((100, 100,), device=device)
        # ...

```

Will execute two tests:

```
test_zeros_like (__main__.TestTorch) ... skipped 'Look at test_zeros_like_cpu, test_zeros_like_cuda results.'
test_zeros_like_cpu (__main__.TestTorch) ... ok
test_zeros_like_cuda (__main__.TestTorch) ... ok
```

It also allows to run each test individually:

```
$> python test/test_torch.py --verbose -k test_zeros_like_cpu
test_zeros_like_cpu (__main__.TestTorch) ... ok
```

Or as a group:
```
python test/test_torch.py --verbose -k test_zeros_like
```

Please do not use older methods such as:

1) `use_cuda` variants:

	```python

	    def _test_random_neg_values(self, use_cuda=False):
	        signed_types = ['torch.DoubleTensor', 'torch.FloatTensor', 'torch.LongTensor',
	                        'torch.IntTensor', 'torch.ShortTensor']
	        for tname in signed_types:
	            res = torch.rand(SIZE, SIZE).type(tname)
	            if use_cuda:
	                res = res.cuda()
	            res.random_(-10, -1)
	            self.assertLessEqual(res.max().item(), 9)
	            self.assertGreaterEqual(res.min().item(), -10)

	    def test_random_neg_values(self):
	        self._test_random_neg_values(self)
	```

	As it requires 3 function declarations and weird `if` statements.

2) `get_all_device_types` variants:

	```python
	    def test_add(self):
	        for device in torch.testing.get_all_device_types():
	            # [res] torch.add([res,] tensor1, tensor2)
	            m1 = torch.randn(100, 100, device=device)
	            v1 = torch.randn(100, device=device)
	# ......
	```

	Generally good, but when it fails, you spent time to figure out if fail is CPU error or CUDA error.
	Also is impossible to filter tests by the name.

3) `_cuda` and `_cpu` variants:

	```python

	    def test_all_any_empty(self):
	        x = torch.ByteTensor()
	# .....

	    @unittest.skipIf(not torch.cuda.is_available(), 'no CUDA')
	    def test_all_any_empty_cuda(self): 
	# .......
	```

	This solution have lots of code duplication and prone to errors.

4) Casting lambdas variants:

	```python
	def _test_dim_reduction(self, cast):
	    # bla bla bla
	    return

	def test_dim_reduction(self):
	    self._test_dim_reduction(self, lambda t: t)
	```

	Casting is slower, also it is hard to read such code.

