## Frequently Asked Questions

#### I’m running into problems with merging my PR. Who should I ask for help?
* Ping someone on the PR (either the person who triaged, or the reviewer(s))
* Come to PyTorch Dev Infra office hours ([https://github.com/pytorch/pytorch/wiki/Dev-Infra-Office-Hours](https://github.com/pytorch/pytorch/wiki/Dev-Infra-Office-Hours)).

#### CI tests failed, what does it mean? 
* Maybe your PR is based off a broken main branch? You can try to rebase your change on top of the latest main branch. You can also see the current status of main branch's CI at https://hud.pytorch.org/.

#### What are the most high risk changes? 
* Anything that touches build configuration is a risky area. Please avoid changing these unless you've had a discussion with the team beforehand.

#### Hey, a commit showed up on my branch, what's up with that? 
* Sometimes another community member will provide a patch or fix to your pull request or branch. This is often needed for getting CI tests to pass.
