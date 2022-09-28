Page Maintainers: @seemethere, @janeyx99, @zengk95, @zainrizvi

Updated on: 8/30/22

Please report any buggy instances to @pytorch/pytorch-dev-infra asynchronously or join our [Office Hours](https://github.com/pytorch/pytorch/wiki/Dev-Infra-Office-Hours) to give in-person feedback or get in-person help!

# Land Checks
Land checks offer extra validation to your PR by rebasing a copy of your changes on top of the latest `viable/strict` branch and ensuring they still pass pull + trunk workflows.

Benefit: You get higher confidence that your PR won't have to be reverted after being merged into master!

Caveat: Slower merges: Once you run the merge command you'll still need to wait for the land checks branch to build and pass all checks

If you have the ciflow/trunk tag on your PR, no extra checks will be run since you've already passed all the checks that would've been attempted.

We are currently rolling out land checks to all of the users in this [list](https://github.com/pytorch/test-infra/blob/main/torchci/lib/bot/rolloutUtils.ts).

If you find rough edges with the land validation:
- Please file an issue to call it out to us!
- You can revert back to the old behavior by invoking `@pytorchmergebot merge -g`, which will only for checks on the PR to pass (which is usually just pull and lint workflows).
- If you believe there's some infra flakiness preventing you from landing, you can also use `-f` and supply a message. 

Additionally, you can add the accept2run label to your PR to run land check signals when your PR gets approved so that the land process doesn't take as long. You can also add accept2ship if you want to land your PR as soon as your PR gets approved.

If you have any feedback or complaints, please reach out to the Pytorch OSS CI team or visit our [Office Hours](https://github.com/pytorch/pytorch/wiki/Dev-Infra-Office-Hours).

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
usage: @pytorchbot merge [-g | -f MESSAGE | -l] [-r [{viable/strict,master}]]

Merge an accepted PR, subject to the rules in .github/merge_rules.json.
By default, this will wait for all required checks (lint, pull) to succeed before merging.

optional arguments:
  -g, --green           Merge when all status checks running on the PR pass. To add status checks, use labels like `ciflow/trunk`.
  -f MESSAGE, --force MESSAGE
                        Merge without checking anything. This requires a reason for auditting purpose, for example:
                        @pytorchbot merge -f 'Minor update to fix lint. Expecting all PR tests to pass'
  -l, --land-checks     Merge with land time checks. This will create a new branch with your changes rebased on viable/strict and run a majority of trunk tests _before_ landing to increase trunk reliability and decrease risk of revert. The tests added are: pull, Lint and trunk. Note that periodic is excluded. (EXPERIMENTAL)
  -r [{viable/strict,master}], --rebase [{viable/strict,master}]
                        Rebase the PR to re run checks before merging.  Accepts viable/strict or master as branch options and will default to viable/strict if not specified.
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

Rebase a PR. Rebasing defaults to the stable viable/strict branch of pytorch.
You, along with any member of the pytorch organization, can rebase your PR.

optional arguments:
  -s, --stable          [DEPRECATED] Rebase onto viable/strict
  -b BRANCH, --branch BRANCH
                        Branch you would like to rebase to
```
## Labeling
You can also add multiple labels using comments with pytorchbot.

```
@pytorchbot label label1,label2,etc
```