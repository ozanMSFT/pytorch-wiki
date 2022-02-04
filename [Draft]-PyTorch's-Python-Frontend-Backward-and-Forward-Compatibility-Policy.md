# Summary

This page describes PyTorch's Python Frontend backwards and forward compatibility policy. This policy lets us provide a modern user experience while minimizing disruption to PyTorch's users and developers. 

# Backwards compatibility (BC)

A version of PyTorch is "backwards compatible (BC)" with previous versions if it can run the same programs those older versions can run. This is an important property, because without it users would have to create new PyTorch programs with every release. In practice, PyTorch may not be completely backwards compatible with previous versions, but there should be few, clearly documented incompatibilities that are easy to address for users and developers between each version.

## Why BC-breaking changes happen

"BC-breaking" changes stop some older PyTorch programs from running on new PyTorch versions, and this can be disruptive to users and developers. These changes are, however, necessary to give PyTorch the best user experience possible, replacing broken, slow, confusing, insufficient or otherwise outdated functionality with modern improvements. For example, PyTorch used to have a torch.fft function, but that has been replaced with the torch.fft module, which offers an easier to use interface and more functionality. 

## Minimizing the disruption of BC-breaking changes

PyTorch makes a best effort to inform users and developers about upcoming BC-breaking changes to stable components (see below), prevent "silent breakages" (where older programs still run on newer PyTorch versions but produce semantically different results), and minimize the disruption they cause. To achieve these, the ideal BC-breaking change is made as follows:

- A warning should be thrown once per process (using `TORCH_WARN_ONCE`) when the deprecated behavior is used.
  - The warning should clearly describe the deprecated behavior and why it is deprecated.
  - The warning should describe when the PyTorch release where the deprecation first takes effect.
  - If possible, the warning should provide a straightforward alternative to the deprecated behavior.
    - Note that alternative behaviors should be stable (more on stability below). And it may be wise to delay a deprecation until a stable alternative is available.
- The warning should appear for 180 days on PyTorch nightlies or two PyTorch releases (whichever is longer). 
- If the new behavior could cause "silent breakages," then the old behavior should throw a runtime error for another 180 days on PyTorch nightlies or two PyTorch releases (whichever is longer).
- The documentation should be updated with a warning that highlights the current status of the deprecation. It should include alternatives, if possible.
- After the deprecation takes effect, the documentation should be updated with a note that describes what changes have occurred and which releases they occurred in. 

Note that it is not always possible to make BC-breaking changes this way. Sometimes, for example, changes have to be reverted because they have unintended consequences, and these reverts can cause BC-breaking changes themselves. Other times the current behavior is so unsafe it should not be used at all and there is no clear alternative to it. Still, all BC-breaking changes should do their best to comport with the above. 

This policy means that PyTorch programs relying on stable components and running without deprecation warnings are likely to continue running as expected for at least 180 days or two more PyTorch releases. And when BC-breaking changes would cause silent breakages this policy provides a window of 360 days or 4 PyTorch releases to safely update. By providing ample warning time, clear alternatives, and prominent documentation we make it easy for users to update their programs with a minimum of fuss and facilitate developers supporting multiple PyTorch versions. 


## What isn't a BC-breaking change

BC-breaking changes prevent old programs from running as expected on newer PyTorch versions. Generally, however, the following are not BC-breaking changes:

* bug fixes (where PyTorch's behavior changes to better reflect its documentation)
* small numerics changes
* numerical accuracy improvements
* changes to PyTorch's internals
* changes to module and operator signatures that don't impact users

This is especially important for developers building on PyTorch to understand. For example, adding a new keyword-only argument with a default value to an operator or module is not a BC-breaking change because PyTorch programs will continue running as before. Systems relying on PyTorch's operators and modules should account for this possibility and not depend on the precise form of PyTorch's internals.

## Prototypes, beta, and stable parts of PyTorch

Different parts of PyTorch are considered to be in a "prototype," "beta," or "stable" state. See [this blog post](https://pytorch.org/blog/pytorch-feature-classification-changes/) for details about these designations. Importantly, BC-breaking changes to prototype and beta components can be made without warning or following any of the above processes. This is necessary to ensure the swift development of new features, and is a reason why users and developers may not want to rely on prototype or beta components.

Current stable behavior should, ideally, not be deprecated in favor of prototype or beta features. The deprecation should generally wait until alternatives to it are stable. 

Prototype components should warn users before use (using `TORCH_WARN_ONCE`) and beta components should provide a clear warning notice in their documentation that they are subject to change at any time without warning.

# Forwards compatibility (FC)

A version of PyTorch is "forwards compatible (FC)" with future versions if programs written for those future versions run in the current version. This is practically impossible to achieve completely because PyTorch is constantly evolving and adding new features which won't work on previous versions. However, a best effort is made for forwards compatibility, and there is a particular case we try hard to support:

* If a program runs on PyTorch with commit X, then the same program serialized using torchscript by a commit of PyTorch no more than two weeks older than X should also run on commit X.

This is a very technical case that is unlikely to come up for most users, especially users who don't use nightly builds, build from source, or use torchscript. Conceptually PyTorch tries to support a two-week torchscript FC window which allows PyTorch users leeway when updating multiple systems using PyTorch. For instance if a company has one system to train PyTorch models and another that runs them using torchscript, then this allows the the version of PyTorch running models to be up to two weeks older than the version that trains them.