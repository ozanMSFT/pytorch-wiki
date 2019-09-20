PyTorch's test framework lets you write tests that can run on all available device types. These "device generic" tests are a great way to ensure operations run properly no matter what hardware PyTorch is using.

### Writing a Device Generic Test

To write a device generic test you have to do three things:

1. Your test should accept two arguments, self and device. The latter will be a string designating a device, like 'cpu,' 'cuda,' or 'xla.' 
2. Decorate your test as appropriate, possibly using the CPU and CUDA-specific decorators defined in test/common_device_type.py.
3. Your test should be in a device generic test class, like `TestTorchDeviceType` in test_torch.py. 

For example, the following Python

```python
class TestTorchDeviceType(TestCase):
  def test_diagonal(self, device):
    ...
```

is translated to

```python
class TestTorchDeviceTypeCPU(TestCase):
  def test_diagonal_cpu(self, device='cpu'):
    ...

class TestTorchDeviceTypeCUDA(TestCase):
  def test_diagonal_cuda(self, device='cuda'):
    ...
```

These tests can be run directly, `python test_torch.py TestTorchDeviceTypeCPU.test_diagonal_cpu`, or filtered using pytest. The command `pytest test_torch.py -k 'test_diagonal'` will run both `test_diagonal_cpu` and `test_diagonal_cuda`. 

See `TestTorchDeviceType` in test_torch.py for examples.

### Writing a Device Generic Test with Dtype Variants

Tests can accept a third argument, 'dtype,' if they use the @dtypes decorator, like so:

```python
@dtypes(torch.half, torch.float, torch.double)
testX(self, device, dtype)
```

This will instantiate variants of testX for each available device type and each specified dtype...

```python
testX_cpu_half(self, 'cpu', torch.half)
testX_cpu_float(self, 'cpu', torch.float)
testX_cpu_double(self, 'cpu', torch.double)
testX_cuda_half(self, 'cuda', torch.half)
...
```

These tests can be run just like device generic tests without dtypes.

### Additional Decorators

`test/common_device_type.py` defines a variety of decorators:

```python
@skipCPUIfNoLapack  # Skips CPU variants of the test if LAPACK is not installed
@skipCPUIfNoMkl     # Skips CPU variants of the test if MKL is not installed
@skipCUDAIfNoMagma  # Skips CUDA variants of the test if MAGMA is not installed
@skipCUDAIfRocm     # Skips CUDA variants of the test if ROCm is being used
@dtypesIfCPU(...)   # Overrides dtypes for CPU test variants
@dtypesIfCUDA(...)  # Overrides dtypes for CUDA test variants
```

Prefer these to less specific decorators like `@skipIfRocm`, because that decorator will skip CPU variants of tests, too. 

### Older Methods (Do Not Use)

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

5) Decorator `@torchtest.test_all_device_types()`, which would only instantiate CPU and CUDA variants of a test.