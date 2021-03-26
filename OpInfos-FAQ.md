See also the [[Writing-tests-in-PyTorch-1.8]] page for an introduction to the terms used here

Here are some tips for when you want to write tests using OpInfos. The basic layout is something like (without PEP8 vertical spacing and imports)

```
class TestAdd(TestCase):
    @ops([OpInfo1, OpInfo2]
    def test_ops(self, device, dtype, op):
        for sample in op.sample_inputs(device, dtype):
            tensors = sample.input
            output = <some func>(*tensors)
            reference = op.ref(*tensors)
            assert_allclose(output, reference, atol=self.precision, rtol=self.precision)

instantiate_device_type_tests(TestAdd, globals())
```
<details>
<summary><b>How many tests will this run?</b></summary>
The `ops` decorator will create a set of tests for each `device` and `OpInfo`.
The `OpInfo.dtype*` and `OpInfo.default_test_dtypes` will be combined with the `device`
to further generate test functions.
</details>
<details>
<summary><b>How do I write a `sample_inputs` method?<b></summary>
TBD, include examples
</details>
<details>
<summary><b>A test failed. How do I reproduce it?<b></summary>
TBD
</details>
<details>
<summary><b>Isn't it bad form to have a loop of values inside the test?</b></summary>
Practicality trumps perfection. The PyTorch CI for each PR is run on many platform variants.
So we end up spending about 30 minutes to build each of 17 different variants (that alone is
about 8 hours of CI build time, but not the subject of this answer), then about 11 full runs
of the tests at around 1 1/2 to 2 hours apiece - another 15 hours of total CI time. Much of this
runs in parallel but it is still a huge undertaking. So we prefer to have smaller, focused tests
that fail early. 
</details>