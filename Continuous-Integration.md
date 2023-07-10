> **Warning** This page is under construction

This document discusses the Continuous Integration (CI) system for PyTorch.

Currently PyTorch utilizes Github Actions for various different CI build/test configurations. We will discuss these related contents in the following sections:

- [CI System Overview](#ci-system-overview)
  - [Basic Configurations](#basic-configurations)
  - [Other Variances](#other-variances)
  - [CI on Commits](#ci-on-commits)
  - [Using labels to change CI on PR](#using-labels-to-change-ci-on-pr)
- [Other](#other)
  - [Sharding](#sharding)
  - [Disabled tests in CI](#disabled-tests-in-ci)
    - [How to disable a test](#how-to-disable-a-test)
    - [Disabling a test for all dtypes/devices](#disabling-a-test-for-all-dtypesdevices)
    - [How to test the disabled test on CI](#how-to-test-the-disabled-test-on-ci)
  - [Reruns/retries](#rerunsretries)
  - [Which commit is used in CI for your PR?](#which-commit-is-used-in-ci-for-your-pr)
  - [CI failure tips on PR](#ci-failure-tips-on-pr)
- [Additional Resources](#additional-resources)
  - [Related wikis](#related-wikis)
  - [Related Repos](#related-repos)


## CI System Overview

PyTorch CI system ensures that proper build/test process should pass on both PRs and master commits. There are several terminologies we would like to clarify:

- *CI workflow*: a CI workflow consist of a collection of CI jobs that are depending on each other. For example: [lint](https://github.com/pytorch/pytorch/blob/5abe454d6c02ac16e2e1fc87500aa46697385501/.github/workflows/lint.yml)
- *CI job*: a CI job consist of a sequence of predefined steps, each of which will execute some specific script to build or test PyTorch. For example: [lintrunner](https://github.com/pytorch/pytorch/blob/5abe454d6c02ac16e2e1fc87500aa46697385501/.github/workflows/lint.yml#L14)
- *CI run*: Refers to all the CI workflows triggered on a single commit (either on PR or on master).
- *CI platform*: we currently have 1 CI platform (CircleCI was used also used previously and some artifacts can be found in the repo.  CircleCI is also used in some other domain libraries).

PyTorch supports many different hardware architectures, operation systems, and accelerator GPUs. Therefore, many different CI workflows run parallel on each commit to ensure that PyTorch can be built and run correctly in different environments and configurations.

### Basic Configurations

**Note** examples are based on the CI workflows at the time of writing.

1. Python versions (e.g. 3.6 - 3.9)
2. OS versions (e.g. Linux xenial/bionic, Windows 10, macos 10.13/10.15)
3. Compute Hardware (e.g. CPU, CUDA10/11, ROCM4)
4. Compiler (GCC 5.4/7/9, Clang5/7/9)

Obviously not every Cartesian product of these combination is being tested. Please refer to the [PyTorch CI HUD](https://hud.pytorch.org/) for more information on all the combinations run currently.

### Other Variances

**Note** variances in configurations can change more rapidly comparing to the basic configurations.

Other than the 4 basic configuration coordinates, we also have some special variances in configurations that we run. These variances are created to test some specific features or to cover some specific test domains. For example:

1. *ASAN build*: to run address sanitizers.
2. *libtorch busilds*: to target CPP APIs.
3. *pure torch build*: to test PyTorch built without Caffe2.
4. *ONNX build*: to test ONNX integration.
5. *XLA/Vulkan build*: to test XLA integration.
6. *Android/IOS build*: to test PyTorch run mobile platforms.

### CI on Commits

Currently there are 2 main categories of CI runs for a single commit:
* pull_request trigger (pull, lint): These are jobs that are always run on every PR and on every commit on main.
* ciflow label trigger: These are jobs that are triggered by adding `ciflow/<workflow name>` tags to the PR.  These can be further divided into:
  * run on every commit in main (trunk, inductor)
  * periodic workflows (periodic, slow)
  * nightlies/binaries: These are usually run on the nightly branch instead of the main branch and generally build and smoke test our binaries.

Each category contains a subset of CI workflows defined in the CI matrix.

We generally consider CI workflows run on commits in main to be the baseline, and we run subsets of this baseline on PRs.

### Using labels to change CI on PR

As mentioned in [CI Matrix](#ci-matrix), PyTorch runs different sets of jobs on PR vs. on main commits.

In order to control the behavior of CI jobs on PR. The most commonly used labels are:
- `ciflow/trunk`: automatically added when `@pytorchbot merge` is invoked.  These tests are run on every commit in master.
- `ciflow/periodic`: runs every 4 hours on master.  Includes jobs that are either expensive or slow to run, such as mac x86-64 tests, slow gradcheck, and multigpu.
- `ciflow/inductor`: runs inductor builds and tests.  This label may be automatically added by our autolabeler if your PR touches certain files.  These jobs are run on every commit in master.
- `ciflow/slow`: runs every 4 hours on master.  Runs tests that are marked as slow.

For a complete definition of every job that is triggered by these labels, as well as other labels that are not listed here, [search for `ciflow/` in the `.github/workflows` folder](https://github.com/search?q=repo%3Apytorch%2Fpytorch+ciflow%2F+path%3A.github%2Fworkflows%2F*.yml&type=code) or run `grep -r 'ciflow/' .github/workflows`.

Additional labels include:
- `keep-going`: test jobs stop at first test failure.  Use this label to keep going after first failure.
- `test-config/<default, distributed, etc>`: only run a specific test config.


## Other

### Sharding
The total time of our entire test suite takes over 24 hours to run if run serially, so we shard and parallelize our tests to decrease this time.  Test job names generally look like `<configuration/architecture information, ex os, python version, compiler version> / test (<test_config>, <shard number>, <total number of shards>, <machine type>, <optional additional information>)`.  For example `linux-bionic-py3.11-clang9 / test (default, 2, 2, linux.2xlarge)` is running the second shard of two shards for the default test config.

Tests are distributed across the number of shards based on how long they take.  Long tests are broken into smaller chunks based on their test times and may show up on different shards as well.  Test time information updates everyday at around 5PM PT, which can cause tests to move to different shards.

Information about what test files are run on each shard can be found by searching the logs for `Selected tests`.

### Disabled tests in CI

Some PyTorch tests are currently disabled due to their flakiness, incompatibility with certain platforms, or other temporary brokenness. We have a system where GitHub issues titled “DISABLED test_a_name” disable desired tests in PyTorch CI until the issues are closed, e.g., #62970. If you are wondering what tests are currently disabled in CI, please check out [disabled-tests.json](https://github.com/pytorch/test-infra/blob/generated-stats/stats/disabled-tests-condensed.json), where these test cases are all gathered.

#### How to disable a test
First, you should never disable a test if you're not sure what you're doing, and only contributors with write permissions can disable tests. Tests are important in validating PyTorch functionality, and ignoring test failures is not recommended as it can degrade user experience. When you are certain that disabling a test is the best option, for example, when it is flaky, make plans to fix the test so that it is not disabled indefinitely.

To disable a test, create an issue with the title `DISABLED test_case_name (test.ClassName)`. A real title example would look like: `DISABLED test_jit_cuda_extension (__main__.TestCppExtensionJIT)`. In the body of the issue, feel free to include any details and logs as you normally would with any issue. It is possible to only skip the test for specific platforms (like rocm, windows, linux, or mac), or for specific test configs (available ones are inductor, dynamo, and slow).  To do this, include a line (case insensitive) in the issue body like so: "\<start of line>Platforms: Mac, Windows\<end of line>."

#### Disabling a test for all dtypes/devices
Currently, you are able to disable all sorts of test instantiations by removing the suffixed device type (e.g., `cpu`, `cuda`, or `meta`) AND dtype (e.g., `int8`, `bool`, `complex`). So given a test `test_case_name_cuda_int64 (test.ClassNameCUDA)`, you can:
1. specifically disable this particular test with `DISABLED test_case_name_cuda_int64 (test.ClassNameCUDA)`
2. disable ALL instances of the test with `DISABLED test_case_name (test.ClassName)`. NOTE: You must get rid of BOTH the device and dtype suffixes AND the suffix in the test suite as well.

#### How to test the disabled test on CI
It is not easy to test these disabled tests with CI since they are automatically skipped. Previous alternatives were to either mimic the test environment locally (often not convenient) or to close the issue and re-enable the test in all of CI (risking breaking trunk CI).

PRs with key phrases like “fixes #55555” or “Close https://github.com/pytorch/pytorch/issues/62359” in their PR bodies or commit messages will re-enable the tests disabled by the linked issues (in this example, #55555 and #62359).

More accepted key phrases are defined by the [GitHub docs](https://docs.github.com/en/issues/tracking-your-work-with-issues/linking-a-pull-request-to-an-issue#linking-a-pull-request-to-an-issue-using-a-keyword).

### Reruns/retries
We have various sources of reruns in our CI to help deal with flakiness:
* Job/workflow reruns: These are done on both main and PR commits.  We have various rules for what jobs should be rerun and when which can be found in test-infra.  Jobs can also be rerun through the Github UI.  Unfortunately, due to Github limitations, jobs can only be rerun after the entire workflow is finished.
* Step reruns: These rerun individual steps in a job and can be identified by steps that use the nick-fields/retry action.
* Test file reruns: These rerun test files in a new subprocess to help with flakiness that results in segfaults, other signal related errors, or issues related to tests changing global state.  These are done 3 times and if the test fails on all 3, the job fails.
* Test reruns: In addition to test file reruns, tests are rerun within a subprocess 3 times.

Additionally, there may be additional sources of retries, for example retrying network calls.

### Which commit is used in CI for your PR?

The code used in your PR is the code in the most recent commit of your PR.  However, the workflow file definitions are a merge between the current `main` brant workflow file definitions and those found in your PR.  This can cause failures due to the merged workflow file referencing a file that might not exist yet and is generally resolved by rebasing.

### CI failure tips on PR

1. Rebasing can resolve failures due to an already red merge base and changes to workflow file definitions.
2. Rerunning jobs can help determine if the failure is flaky.
3. Checking the [HUD home page](https://github.com/pytorch/pytorch/wiki/Using-hud.pytorch.org#hud-home-page) can tell you if CI is in a bad state and also what failures are currently common.
4. Use the [HUD](https://github.com/pytorch/pytorch/wiki/Using-hud.pytorch.org#pull-requests-and-commits) to view the log viewer and log classifier to see what we think is the most likely cuase of your failure.


## Additional Resources

A compilation of dashboards and metrics relating to CI could be found in [HUD metrics page](https://hud.pytorch.org/metrics)

### Related wikis
* [Running and writing tests](https://github.com/pytorch/pytorch/wiki/Running-and-writing-tests)
* [Docker image build on CircleCI](https://github.com/pytorch/pytorch/wiki/Docker-image-build-on-CircleCI)
### Related Repos

* [pytorch/builder](https://github.com/pytorch/builder)
* [pytorch/test-infra](https://github.com/pytorch/test-infra)
* [pytorch/pytorch-ci-hud](https://github.com/pytorch/pytorch-ci-hud)
