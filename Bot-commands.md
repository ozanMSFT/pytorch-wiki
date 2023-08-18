Page Maintainers: @seemethere, @janeyx99, @zengk95, @zainrizvi

Updated on: 8/30/22

Please report any buggy instances to @pytorch/pytorch-dev-infra asynchronously or join our [Office Hours](https://github.com/pytorch/pytorch/wiki/Dev-Infra-Office-Hours) to give in-person feedback or get in-person help!

# PyTorchBot Help
```
usage: @pytorchbot [-h] {merge,revert,rebase,label,drci} ...

In order to invoke the bot on your PR, include a line that starts with
@pytorchbot anywhere in a comment. That line will form the command; no
multi-line commands are allowed.

Example:
    Some extra context, blah blah, wow this PR looks awesome

    @pytorchbot merge

optional arguments:
  -h, --help            Show this help message and exit.

command:
  {merge,revert,rebase,label,drci}
    merge               Merge a PR
    revert              Revert a PR
    rebase              Rebase a PR
    label               Add label to a PR
    drci                Update Dr. CI
```
## Merge
```
usage: @pytorchbot merge [-f MESSAGE | -ic] [-r [{viable/strict,main}]]

Merge an accepted PR, subject to the rules in .github/merge_rules.json.
By default, this will wait for all required checks (lint, pull) to succeed before merging.

optional arguments:
  -f MESSAGE, --force MESSAGE
                        Merge without checking anything. This requires a reason for auditting purpose, for example:
                        @pytorchbot merge -f 'Minor update to fix lint. Expecting all PR tests to pass'
  -i, --ignore-current
                        Merge while ignore the currently failing jobs.  If there are no pending checks, use -f/--force since this will fail.
  -r [{viable/strict,master}], --rebase [{viable/strict,master}]
                        Rebase the PR to re run checks before merging.  Accepts viable/strict or master as branch options and will default to viable/strict if not specified.
```
## Revert
```
usage: @pytorchbot revert -m MESSAGE -c
                          {nosignal,ignoredsignal,landrace,weird,ghfirst}

Revert a merged PR. This requires that you are a Meta employee.

Example:
  @pytorchbot revert -m="This is breaking tests on trunk. hud.pytorch.org/" -c=nosignal

optional arguments:
  -m MESSAGE, --message MESSAGE
                        The reason you are reverting, will be put in the commit message. Must be longer than 3 words.
  -c {nosignal,ignoredsignal,landrace,weird,ghfirst}, --classification {nosignal,ignoredsignal,landrace,weird,ghfirst}
                        A machine-friendly classification of the revert reason.
```
## Rebase
```
usage: @pytorchbot rebase [-s | -b BRANCH]

Rebase a PR. Rebasing defaults to the stable viable/strict branch of pytorch.
Repeat contributor may use this command to rebase their PR.

optional arguments:
  -s, --stable          [DEPRECATED] Rebase onto viable/strict
  -b BRANCH, --branch BRANCH
                        Branch you would like to rebase to
```
## Labeling
You can also add multiple labels using comments with pytorchbot.
```
usage: @pytorchbot label labels [labels ...]

Adds label to a PR

positional arguments:
  labels  Labels to add to given Pull Request
```
## Refresh Dr. CI comment
The Dr. CI comment (the first comment left at the top of your PR) refreshes ~every 15 minutes by default. If you ever want it to refresh immediately, you can run the following command to trigger the refresh
```
usage: @pytorchbot drci

Update Dr. CI. Updates the Dr. CI comment on the PR in case it's gotten out of sync with actual CI results.
```


# Other

Upon calling `@pytorchbot merge`, we add the `ciflow/trunk` label to your PR which triggers trunk workflows to run more tests.  This is v2 of our landchecks.

See [Using Github label to control CI behavior on PR](https://github.com/pytorch/pytorch/wiki/Running-and-writing-tests#using-github-label-to-control-ci-behavior-on-pr) for more information about ciflow labels.

# Deprecated
<details><summary>Expand to see old features</summary>


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

If you have any feedback or complaints, please reach out to the Pytorch OSS CI team or visit our [Office Hours](https://github.com/pytorch/pytorch/wiki/Dev-Infra-Office-Hours).

</details>
