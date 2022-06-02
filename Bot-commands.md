Page Maintainers: @seemethere, @janeyx99, @zengk95

Updated on: Wed, Jun 1, 2022

Please report any buggy instances to @pytorch/pytorch-dev-infra asynchronously or join our [Office Hours](https://github.com/pytorch/pytorch/wiki/Dev-Infra-Office-Hours) to give in-person feedback or get in-person help!

# @pytorchbot commands

In order to invoke the bot on your PR, simply include a line that starts with `@pytorchbot` anywhere in a comment. That line will form the command; no multi-line commands are allowed. For example:
```
Some extra context, blah blah, wow this PR looks awesome

@pytorchbot merge
```

## CI Flow
By default, the Lint and pull workflows should run on every push to your pull request. If that is not the case, tag @pytorch/pytorch-dev-infra with the instance. To trigger a set of non-default tests on your PR, add corresponding [labels](https://github.com/pytorch/pytorch/labels?q=ciflow) for the workflows you'd like to rerun (e.g., ciflow/trunk or ciflow/scheduled). 

## Merging through GH1 (Beta)
We are in the process of prototyping our GitHub First initiative, which will allow certain pull requests based on the upstream repo (not a fork) to be merged through GitHub first, then imported internally into Meta. The requirements for such PRs are described in [our merge rules](https://github.com/pytorch/pytorch/blob/master/.github/merge_rules.json) where files fitting the specified patterns approved by the specified maintainers are allowed to merge directly with the following comment:

```
usage: @pytorchbot merge [-g] [-a] [-f]

optional arguments:
  -g, --green      Merge when required status checks pass. Currently, we only
                   require lint and builds to pass.
  -a, --all-green  Merge when all status checks pass
  -f, --force      Merge without checking anything. ONLY USE THIS FOR CRITICAL
                   FAILURES.
```

Examples:
```
@pytorchbot merge
@pytorchbot merge -g
```
## Reverting 
Similar to merging, we have a command to revert a PR, though it requires you be a Meta employee at the moment. You can always submit a PR to back out a change and merge with the commands above. Revert requires you to provide both a message and classification for record keeping purposes.

```
usage: @pytorchbot revert -m MESSAGE
                          [-c {nosignal,ignoredsignal,landrace,weird,ghfirst}]

optional arguments:
  -m MESSAGE, --message MESSAGE
                        The reason you are reverting, will be put in the
                        commit message.
  -c {nosignal,ignoredsignal,landrace,weird,ghfirst}, --classification {nosignal,ignoredsignal,landrace,weird,ghfirst}
                        A machine-friendly classification of the revert
                        reason.
```

Examples:

```
@pytorchbot revert -m="This is breaking tests on trunk. hud.pytorch.org/" -c=nosignal
```
## Labeling
You can also add multiple labels using comments with pytorchbot.

```
@pytorchbot label label1,label2,etc
```

## Rebase
Rebasing is useful you're reviewing an older PR and want to get new signal: you might want to rebase to get the most up to date tests and re run the CI. Rebasing defaults to the default branch of pytorch/pytorch, which currently is master. You, along with any member of the pytorch organization, can rebase your PR.

```
usage: @pytorchbot rebase [-s]

optional arguments:
  -s, --stable  Rebase to viable/strict
```

Examples:
```
@pytorchbot rebase -s
```

<details>
<summary> deprecated commands </summary>

## @pytorchbot commands deprecated
The following commands are deprecated, you might find them used in the previous PRs, but due to the fundamental CI system changes, these commands do not work anymore. 

```
# Deprecated chatops commands

@pytorchbot retest this please
```

</details>