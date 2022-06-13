Page Maintainers: @seemethere, @janeyx99, @zengk95

Updated on: Wed, Jun 1, 2022

Please report any buggy instances to @pytorch/pytorch-dev-infra asynchronously or join our [Office Hours](https://github.com/pytorch/pytorch/wiki/Dev-Infra-Office-Hours) to give in-person feedback or get in-person help!

# PyTorchBot Help
```
usage: @pytorchbot [-h] {merge,revert,rebase} ...

In order to invoke the bot on your PR, include a line that starts with
@pytorchbot anywhere in a comment. That line will form the command; no
multi-line commands are allowed.

Example:
    Some extra context, blah blah, wow this PR looks awesome

    @pytorchbot merge

optional arguments:
  -h, --help            Show this help message and exit.

command:
  {merge,revert,rebase}
    merge               Merge a PR
    revert              Revert a PR
    rebase              Rebase a PR
```
## Merge
```
usage: @pytorchbot merge [-g | -f]

Merge an accepted PR, subject to the rules in .github/merge_rules.json.
By default, this will wait for all required checks to succeed before merging.

optional arguments:
  -g, --green  Merge when *all* status checks pass.
  -f, --force  Merge without checking anything. ONLY USE THIS FOR CRITICAL FAILURES.
```
## Revert
```
usage: @pytorchbot revert -m MESSAGE [-c {nosignal,ignoredsignal,landrace,weird,ghfirst}]

Revert a merged PR. This requires that you are a Meta employee.

Example:
  @pytorchbot revert -m="This is breaking tests on trunk. hud.pytorch.org/" -c=nosignal

optional arguments:
  -m MESSAGE, --message MESSAGE
                        The reason you are reverting, will be put in the commit message.
  -c {nosignal,ignoredsignal,landrace,weird,ghfirst}, --classification {nosignal,ignoredsignal,landrace,weird,ghfirst}
                        A machine-friendly classification of the revert reason.
```
## Rebase
```
usage: @pytorchbot rebase [-s | -b BRANCH]

Rebase a PR. Rebasing defaults to the default branch of pytorch (master).
You, along with any member of the pytorch organization, can rebase your PR.

optional arguments:
  -s, --stable          Rebase to viable/strict
  -b BRANCH, --branch BRANCH
                        Branch you would like to rebase to
```
## Labeling
You can also add multiple labels using comments with pytorchbot.

```
@pytorchbot label label1,label2,etc
```