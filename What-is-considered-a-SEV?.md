[File a SEV](https://github.com/pytorch/pytorch/issues/new?assignees&labels=ci%3A+sev&template=ci-sev.md)

# Why do we [file SEVs](https://github.com/pytorch/pytorch/issues/new?assignees&labels=ci%3A+sev&template=ci-sev.md)?

1. Alert devs that something is currently wrong with the CI and they may see failures that they’re not responsible for
2. Track developer facing issues so that we can appropriately prioritize fixing (without SEVs these can get forgotten)

# What’s considered a SEV?

When there is a widespread CI breakage that is not resolvable by doing a revert (e.g. it’s an issue with the underlying CI infra), you should file a SEV using the [CI SEV issue template](https://github.com/pytorch/pytorch/issues/new?assignees&labels=ci%3A+sev&template=ci-sev.md). 

## Rules of thumb 

* If it’s bad enough that people multiple people will ask us about it on any of our contributor communication channels
* If it affects more than ~10% of users and might last over 30 minutes, it’s a SEV
* If we’re expecting people to force push to work around it, it’s a SEV

If the above still leaves it ambiguous, we default to considering it a SEV.

If the sev is severe enough (generally defined as "the CI is likely to miss legitimate failures", the template includes instructions on how to make it *merge blocking* to prevent contributors from accidentally merging broken code.

# During a SEV
[HUD](http://hud.pytorch.org) and the Dr. CI comment at the top of the PR will both explain that there's a SEV ongoing and link to the active sev.

If the SEV is marked as merge blocking, @pytorchbot will default to preventing PRs from being merged