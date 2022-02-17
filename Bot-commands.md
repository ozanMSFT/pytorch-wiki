Page Maintainers: @zhouzhuojie, @seemethere, @janeyx99

Updated on: Thu, Feb 17, 2022

# @pytorchbot commands

## Re-running workflows with ciflow
First, add the labels for the workflows you'd like to rerun (e.g., ciflow/cuda or ciflow/scheduled), then
```
# rerun all workflows
@pytorchbot ciflow rerun
```

## Merging through GH1 (Beta)
We are in the process of prototyping our GitHub First initiative, which will allow certain pull requests based on the upstream repo (not a fork) to be merged through GitHub first, then imported internally into Meta. The requirements for such PRs are described in [our merge rules](https://github.com/pytorch/pytorch/blob/master/.github/merge_rules.json) where files fitting the specified patterns approved by the specified maintainers are allowed to merge directly with the following comment:

```
@pytorchbot merge this please
```


<details>
<summary> deprecated commands </summary>

## @pytorchbot commands deprecated
The following commands are deprecated, you might find them used in the previous PRs, but due to the fundamental CI system changes, these commands do not work anymore. 

```
# Deprecated chatops commands

@pytorchbot retest this please
@pytorchbot rebase this please
@pytorchbot label this please
```

</details>