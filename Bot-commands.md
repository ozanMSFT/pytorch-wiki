Page Maintainers: @zhouzhuojie

Updated on: Thursday, Aug 12, 2021

# ChatOps

## @pytorchbot commands live
Stay tuned. Due to the underlying CI system migrations, we will introduce new @pytorchbot commands to orchestrate PR workflows.

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


