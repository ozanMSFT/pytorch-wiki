Thank you for considering contributing to PyTorch. The contribution process for PyTorch is similar to most open source projects on Github, involving a healthy amount of open discussion between the maintainers, core developers and the broader community.

#### Table of Contents
- [All the ways to contribute](#all-the-ways-to-contribute)
- [Getting Started with Documentation changes](#getting-started-with-documentation-changes)
- [Getting Started with Pull Requests](#getting-started-with-pull-requests)
  - [Familiarize yourself with the codebase](#familiarize-yourself-with-the-codebase)
  - [Setup the developer environment](#setup-the-developer-environment)
  - [Submitting a change](#submitting-a-change)
  - [Keep up with PyTorch Developments](#keep-up-with-pytorch-developments)

----

## All the ways to contribute
There are many ways to contribute to the PyTorch project, and we value them all. Most open source contributors start with small improvements, and ramp up the scale of their work as they become more familiar with the project and the community. 

<details>
<summary><h3><b>Community Discussions</b></h3></summary>
PyTorch has an active community of users, researchers, and developers. We tend to congregate on the <a href="https://discuss.pytorch.org">PyTorch User Forum</a> and <a href="https://dev-discuss.pytorch.org">PyTorch Developer Forum</a>. Participating in conversations here is a great way to learn about PyTorch and contribute your expertise to the community. 
<br> 
PyTorch developers also engage in discussions around design and feature-level changes at the PyTorch RFC repo and Slack channel (<a href="Sharing-design-documents-for-discussion">read more</a>).

</details>

<details>
<summary><h3><b>Tutorials</b></h3></summary>
If you are already a user of PyTorch, good places to start contributing are the PyTorch tutorials. The PyTorch tutorials are mainly authored by community members, and we always welcome new contributions or updates to existing tutorials. Learn more about <a href="https://github.com/pytorch/tutorials/#contributing">contributing to PyTorch tutorials</a>.

</details>

<details>
<summary><h3><b>Documentation</b></h3></summary>
We aim to produce high-quality documentation, but typos or other inaccuracies may creep in. If you find something to fix or improve, please send in a pull request. The <a href="#getting-started-with-documentation-changes">docs guide</a> contains resources for you to start submitting documentation updates.

</details>

<details>
<summary><h3><b>Reporting Issues</b></h3></summary>
If you happen to run into some unexpected behavior, you can help by creating an issue (if a similar one doesn't already exist on the tracker). Use the Bug Report template and supply as much information as you can, and any additional insights/guesses you might have. See <a href="Finding-or-Creating-Issues">Finding or Creating Issues</a> to get started.

</details>

<details>
<summary><h3><b>Reproducing Issues</b></h3></summary>
Another valuable way to contribute is by <a href="https://github.com/pytorch/pytorch/labels/needs%20reproduction">reproducing open issues</a>. Sometimes the problematic behavior may be isolated to specific environments, repro'ing it helps developers identify the cause and troubleshoot faster.

</details>

<details>
<summary><h3><b>Proposing New Features</b></h3></summary>
We welcome ideas for new features in PyTorch! A great way to share it with the community is by <a href="How-to-propose-feature-changes-to-PyTorch">drafting an RFC</a> (request for comments), especially if you have fleshed out the design and would like it reviewed. For more casual ideas, the <a href="https://dev-discuss.pytorch.org">Dev-Discuss</a> forum is a good place to start.

</details>

<details>
<summary><h3><b>Submitting Pull Requests</b></h3></summary>
Fixing existing issues or implementing new features require changes to the codebase. We manage merging changes via Github Pull Requests. You will need to set up your developer environment, find an issue to work upon, and submit your changes for review. The <a href="#getting-started-with-pull-requests">PR guide</a> below contains resources for you to start submitting your changes to PyTorch.

</details>

<details>
<summary><h3><b>Advocate for PyTorch</b></h3></summary>
As a PyTorch user, you are already a valued member of our community. Using PyTorch in your projects, examples, demos, workshops, and blogs goes a long way in raising awareness for PyTorch and the community. Please reach out to <a href="mailto:marketing@pytorch.org">marketing@pytorch.org</a> for any support with sharing your work.

</details>


----


## Getting Started with Documentation changes
[PyTorch documentation](https://pytorch.org/docs) is generated from docstrings in the python source using Sphinx. This generated HTML is copied to the [`docs`](https://github.com/pytorch/pytorch/tree/main/docs) folder in the main branch of [pytorch.github.io](https://github.com/pytorch/pytorch.github.io/tree/master/docs), and is then served via GitHub pages. 

You need to [build and install](#setup-the-developer-environment) PyTorch before you can develop on the docs. 

Here are some resources for developing PyTorch docs:
- [Building the docs](https://github.com/pytorch/pytorch/blob/main/CONTRIBUTING.md#building-documentation)
- [Previewing changes to docs](https://github.com/pytorch/pytorch/blob/main/CONTRIBUTING.md#previewing-documentation-on-prs)
- [Testing docs](https://github.com/pytorch/pytorch/blob/main/CONTRIBUTING.md#adding-documentation-tests)

-----

## Getting Started with Pull Requests
Here are some resources that can help you contribute pull requests:

### Familiarize yourself with the codebase
- [[Core Frontend Onboarding]]
- [Codebase structure](https://github.com/pytorch/pytorch/blob/main/CONTRIBUTING.md#codebase-structure)
- [Developer docs and notes](https://github.com/pytorch/pytorch/wiki#developer-docs)
- [Workflow docs and notes](https://github.com/pytorch/pytorch/wiki#workflow-docs)

### Setup the developer environment
- [[Install Prerequisites and Dependencies|Developer Environment Setup]]
- [[Fork, clone, and checkout the PyTorch source|Fork Clone and Checkout]]
- [[Build PyTorch from source|Build-PyTorch]]
    - [[Debugging your PyTorch build|Debugging PyTorch Build]]
- [[Tips for developing PyTorch|Development Tips]]
- [[PyTorch Workflow Git cheatsheet|PyTorch Workflow Cheatsheet]]

### Submitting a change
- [[Overview of the Pull Request Lifecycle]]
- [[Finding Or Creating Issues]]
  - [[How to propose feature changes to PyTorch]]
- [[Pre Commit Checks]]
- [[Create a Pull Request]]
- [[Typical Pull Request Workflow]]
- [[Pull Request FAQs]]
- [[Getting Help as a Contributor]]

### Keep up with PyTorch Developments
- [[Design Discussions|Sharing design documents for discussion]]
- [[Version Compatibility Matrix|PyTorch Versions]]
