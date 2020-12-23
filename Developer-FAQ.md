## PRs

### My PR hasn't been reviewed in awhile, what should I do? 

You can and should ping reviewers by ccing them in a comment if they haven't responded to a review request in 2 full working days. For example, if you request a review on Friday, you can ping your reviewers on Wednesday of the next week. 

### If I'm editing the documentation, how do I see the formatted doc?

See [here](https://github.com/pytorch/pytorch/blob/master/CONTRIBUTING.md#building-documentation) for instructions on building the docs locally.

If you've submitted a PR, click the "Details" link next to the pytorch_python_doc_build, then go to the "Artifacts" tab. It has links to the documentation built for your PR. 

### My PR caused a build to fail, but it's not usually run in the Github CI. How do I test my PR against EVERY build?

Push your changes to a pytorch/pytorch branch named ci-all/<your name here>. That will trigger all the test builds, not just the ones that typically run.

### My PR was rejected for being "too small" &mdash; what gives!?

Each PR takes costly machine and developer time. "Small" PRs that may correct a spelling error or making a tiny grammar change are simply not worth the cost.

## PyTorch Numerics

### Does PyTorch distinguish between different types of NaN like C++ does?

No. PyTorch treats NaNs like Python does: there is only one floating point NaN and a complex number is NaN if either its real part is NaN, its imaginary part is NaN, or both parts are NaN. 

## Warnings

### When should I TORCH_WARN vs. TORCH_WARN_ONCE?

PyTorch offers both [TORCH_WARN](https://github.com/pytorch/pytorch/blob/4f538a2ba48afeb2a2a1f3b6e01b1ec461d4a5ed/c10/util/Exception.h#L391) and [TORCH_WARN_ONCE](https://github.com/pytorch/pytorch/blob/4f538a2ba48afeb2a2a1f3b6e01b1ec461d4a5ed/c10/util/Exception.h#L402). The latter, as the name suggests, will cause a warning to be triggered one time per process, while TORCH_WARN will throw a warning every time.

Generally you should use TORCH_WARN_ONCE. Warning the user every time a behavior occurs is often onerous, especially if the warning occurs in a network's training loop. Only use TORCH_WARN in the following situations:

- a user opted-in to the warnings, like by enabling anomaly mode
- a non-critical operation failed

For all other situations, like deprecation warnings and behavior that may result in crashes, use TORCH_WARN_ONCE.
