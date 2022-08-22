# Want to make a feature change to PyTorch?
Over half the commits made to pytorch every week come from the wider open source community.  Sometimes researchers have a certain feature that they want to see implemented, companies have hardware they want to support, or a developer found a bug they really need fixed.

With such a large number of commits coming in, PyTorch needs a process for managing it all to keep the codebase maintainable. For smaller changes, like a five line bug fix, this takes the form of a regular PR review.  Make a change, submit a PR, and a core pytorch contributor will review it soon.

But sometimes you’ll want to contribute a larger change, like an enhancement to an existing function or even a brand new feature. Submitting a 1,000 line PR for a feature no one has heard about rarely results in a good experience (for neither the reviewer nor the coder).

For larger changes like this, we have a more indepth process that’s similar in spirit to a design review. It ensures that the feature you’re working on becomes something the core owners are happy to accept and maintain going forward.

# The Request for Comments
To propose a new feature, you’ll submit a Request For Comments (RFC).  This RFC is basically a design proposal where you can share a detailed description of what change you want to make, why it’s needed, and how you propose to implement it.

It’s easier to make changes while your feature is in the ideation phase vs the PR phase, and this doc gives core maintainers an opportunity to suggest refinements before you start code.  For example, they may know of other planned efforts that your work would otherwise collide with, or they may suggest implementation changes that make your feature more broadly usable.

Here are [detailed instructions on how to submit an RFC](https://github.com/pytorch/rfcs/blob/master/README.md)