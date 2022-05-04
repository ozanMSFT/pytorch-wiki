Page Maintainers: @zhouzhuojie, @seemethere, @janeyx99

Updated on: Tue, May 3, 2022

# @pytorchbot commands

## CI Flow
First, add the [labels](https://github.com/pytorch/pytorch/labels?q=ciflow) for the workflows you'd like to rerun (e.g., ciflow/cuda or ciflow/scheduled), then
```
# rerun all workflows
@pytorchbot ciflow rerun
```

## Merging through GH1 (Beta)
We are in the process of prototyping our GitHub First initiative, which will allow certain pull requests based on the upstream repo (not a fork) to be merged through GitHub first, then imported internally into Meta. The requirements for such PRs are described in [our merge rules](https://github.com/pytorch/pytorch/blob/master/.github/merge_rules.json) where files fitting the specified patterns approved by the specified maintainers are allowed to merge directly with the following comment:

```
# For normal merging
@pytorchbot merge this

# If we need to force merge
@pytorchbot force merge this
```

## Reverting 
Similar to merging, we have a command to revert a PR. 

```
@pytorch bot revert this <Revert Reason Greater than 3 words>
```

## Rebase (Beta)
Rebasing is useful you're reviewing an older PR and want to get new signal, you might want to rebase to get the most up to date tests and re run the CI. 

```
@pytorchbot rebase this
```

## Labeling
You can also add multiple labels using comments with pytorchbot.

```
@pytorchbot label label1,label2,etc
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