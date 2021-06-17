# Intro

Testing is an important part of developing PyTorch. Good tests ensure both that:

- operations in PyTorch correctly implement their behavior and that
- changes to PyTorch do not change accidentally change these behaviors.

This article explains PyTorch's existing testing tools to help you write effective and efficient tests.

# Test file organization

PyTorch's test suites are located under [pytorch/test](https://github.com/pytorch/pytorch/tree/master/test).

Code for generating tests and testing helper functions are located under [pytorch/torch/testing/_internal](https://github.com/pytorch/pytorch/tree/master/torch/testing/_internal).

# Running PyTorch's test files

Most PyTorch test files can be run using unittest (the default) or pytest. 

To run a test suite, like test_torch.py, using unittest:

```
python test_torch.py
```

Unittest-specific arguments can be appended to this command. For example, to run only a specific test:

```
python test_torch.py <TestClass>.<TestName>
```

Other commonly useful options are `-k`, which specifies a string to filter the tests, and `-v`, which runs the test suite in "verbose" mode. For example,

```
pytest test_torch.py -k cpu
```

Will run all the tests in test_torch.py with "cpu" in their name.

In addition to unittest and pytest options, PyTorch's test suite also understands the following environment variables:

- PYTORCH_TEST_WITH_SLOW, if set to 1 this will run tests marked with the @slowTest decorator (default=0)
- PYTORCH_TEST_SKIP_FAST, if set to 1 this will skip tests NOT marked with the @slowtest decorator (default=0)
- PYTORCH_TEST_WITH_SLOW_GRADCHECK, if set to ON this use PyTorch's slower (but more accurate) gradcheck mode (default=OFF)
- PYTORCH_TESTING_DEVICE_ONLY_FOR, run tests for ONLY the device types listed here (like 'cpu' and 'cuda') 
- PYTORCH_TESTING_DEVICE_EXCEPT_FOR, run tests for all device types EXCEPT FOR the device types listed here
- PYTORCH_TEST_SKIP_NOARCH, if set to 1 this will all noarch tests (default=0)

For instance,

```
PYTORCH_TEST_WITH_SLOW=1 python test_torch.py
```

Will run the tests in test_torch.py, including those decorated with @slowTest.

# Common test utilities

Use the test case's [assertEqual](https://github.com/pytorch/pytorch/blob/0e7b5ea6c003b763603d8ca0fe12f476fdf9bf32/torch/testing/_internal/common_utils.py#L1277) to compare objects for equality.

Prefer using [make_tensor](https://github.com/pytorch/pytorch/blob/0e7b5ea6c003b763603d8ca0fe12f476fdf9bf32/torch/testing/_internal/common_utils.py#L1744) when generating test tensors over tensor creation ops like _torch.randn_.

# PyTorch's test generation functionality

PyTorch's test framework lets you instantiate test templates for different operators, datatypes (dtypes), and devices to improve test coverage. It is recommended that all tests be written as templates, whether it's necessary or not, to make it easier for the test framework to inspect the test's properties. See [this comment](https://github.com/pytorch/pytorch/blob/0e7b5ea6c003b763603d8ca0fe12f476fdf9bf32/torch/testing/_internal/common_device_type.py#L25) for details on writing test templates.

# OpInfos

OpInfos are used to automatically generate a variety of operator tests from metadata. See [this comment](https://github.com/pytorch/pytorch/blob/0e7b5ea6c003b763603d8ca0fe12f476fdf9bf32/torch/testing/_internal/common_methods_invocations.py#L183) for details on writing OpInfos and what tests they generate. If you're adding a new operator to the torch, torch.special, torch.fft, or torch.linalg namespaces you should write an OpInfo for it so it's tested properly.








