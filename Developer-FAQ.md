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

## Warnings

### When should I TORCH_WARN vs. TORCH_WARN_ONCE?

PyTorch offers both [TORCH_WARN](https://github.com/pytorch/pytorch/blob/4f538a2ba48afeb2a2a1f3b6e01b1ec461d4a5ed/c10/util/Exception.h#L391) and [TORCH_WARN_ONCE](https://github.com/pytorch/pytorch/blob/4f538a2ba48afeb2a2a1f3b6e01b1ec461d4a5ed/c10/util/Exception.h#L402). The latter, as the name suggests, will cause a warning to be triggered one time, while TORCH_WARN will throw a warning every time.

Generally you should use TORCH_WARN_ONCE. Warning the user every time a behavior occurs is often onerous, especially if the warning occurs in a network's training loop. TORCH_WARN is appropriate only if the warning may occur in contexts so different that a user could not infer the the warning would be triggered after reading the first warning. Let's look at a few examples that clarify this principle. 


`TORCH_WARN_ONCE("Casting complex values to real discards the imaginary part");`

This is a good use of TORCH_WARN_ONCE. Once a user has been warned that this behavior occurs they should be able to infer when it's occurring. Warning them each time it happens is unnecessary. 


`TORCH_WARN("Use of masked_fill_ on expanded tensors is deprecated...`

This is a poor use of TORCH_WARN, and TORCH_WARN_ONCE is preferred when warning a user about deprecated behavior. Once a user knows the behavior is deprecated they don't need to be told again and again and again.


`TORCH_WARN("An output with one or more elements was resized since it had...`

This is a good use of TORCH_WARN. This warning [occurs in a helper function](https://github.com/pytorch/pytorch/blob/dae94ed02299cd1e9840ce6e49ea142b4d118b2d/aten/src/ATen/native/Resize.cpp#L12) that can be called from multiple different contexts. This particular behavior of resizing tensors given to `out=` can also be tricky to identify and rarely reflects the user's intent, unlike casting complex tensors to floating point, which is likely an intentional act. 





