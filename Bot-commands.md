Page Maintainers: @zhouzhuojie, @seemethere

Updated on: Monday, Nov 15, 2021

# @pytorchbot commands

## Re-running workflows with ciflow
```
# rerun all workflows
@pytorchbot ciflow rerun
# Run with specific labels
@pytorchbot ciflow rerun -l ciflow/cuda -l ciflow/scheduled
```

<details>
<summary> deprecated commands </summary>

## @pytorchbot commands deprecated
The following commands are deprecated, you might find them used in the previous PRs, but due to the fundamental CI system changes, these commands may or may not work anymore. 

```
# Deprecated chatops commands

@pytorchbot retest this please
@pytorchbot rebase this please
@pytorchbot label this please
@pytorchbot merge this please
```

Specifically, `@pytorchbot retest this please` can still trigger a single jenkins job (`pr/pytorch-linux-bionic-rocm4.2-py3.6`) on PR. Note that most of the tests have been migrated to circleci or github action, these commands may not work for anything other than the jenkins job.

</details>