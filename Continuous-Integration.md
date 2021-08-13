> **Warning** This page is under construction

This document discusses the Continuous Integration (CI) system for PyTorch. 

Currently PyTorch utilizes Github Action, CircleCI and Jenkins CI for various different CI build/test configurations. We will discuss these related contents in the following sections:

- [CI System Overview](#ci-system-overview)
  - [Github Actions](#github-actions)
  - [CircleCI](#circleci)
  - [Jenkins](#jenkins)
- [CI Matrix](#ci-matrix)
  - [Basic Configurations](#basic-configurations)
  - [Other Variances](#other-variances)
- [Entrypoint for CI](#entrypoint-for-ci)
  - [Build System](#build-system)
  - [Test System](#test-system)
- [What is CI testing and When](#what-is-ci-testing-and-when)
  - [CI workflow on PR](#ci-workflow-on-pr)
  - [CI workflow on master commits](#ci-workflow-on-master-commits)
  - [Change CI workflow behavior on PR](#change-ci-workflow-behavior-on-pr)
    - [Using Github Labels](#using-github-labels)
    - [Using CIFlow](#using-ciflow)
- [Other Topics](#other-topics)
  - [CI Internals](#ci-internals)
  - [Binary Builds](#binary-builds)
  - [Docker Builds](#docker-builds)
  - [Other Related Repos](#other-related-repos)
  - [Which commit is used in CI for your PR?](#which-commit-is-used-in-ci-for-your-pr)
  - [CI failure tips on PR](#ci-failure-tips-on-pr)


## CI System Overview

PyTorch CI system ensures that proper build/test process should pass on both PRs and master commits. There are several terminologies we would like to clarify:

- CI job: a CI job consist of a sequence of predefined steps, each of which will execute some specific script to build or test PyTorch. For example: 
- CI workflow: a CI workflow consist of a collection of CI jobs that are depending on each other. For example:
- CI result on a single commit: refers to all the CI workflows triggered on a single commit (either on PR or on master). For example:
- CI platform: we currently have 3 CI platforms (Github Actions, CirlceCI, Jenkins). 

PyTorch supports many different hardware architectures, operation system, accelerator GPU. Therefore, many different CI workflows run parallel on each commit to ensure that PyTorch can be built and run correctly in different environments and configurations. See [CI Matrix](#ci-matrix) section for more info.

For historic reasons we currently have 3 different CI platforms and there are dozens of CI jobs run on these 3 platforms. For each platform specific information please refer to the [CI Internals](#ci-internals) section for more info.

## CI Matrix

### Basic Configurations

### Other Variances

## Entrypoint for CI

### Build System

### Test System

See ["Running and writing tests" page](https://github.com/pytorch/pytorch/wiki/Running-and-writing-tests) for information.

## What is CI testing and When

### CI workflow on PR

### CI workflow on master commits

### Change CI workflow behavior on PR

#### Using Github Labels

See ["how to use GitHub labels" section on the "Running and writing tests" page](https://github.com/pytorch/pytorch/wiki/Running-and-writing-tests#using-github-label-to-control-ci-behavior-on-pr) for information.

#### Using CIFlow

## Other Topics

### CI Internals

### Binary Builds

### Docker Builds

See: https://github.com/pytorch/pytorch/wiki/Docker-image-build-on-CircleCI for more information.

### Other Related Repos

#### [pytorch/builder](https://github.com/pytorch/builder)
#### [pytorch/test-infra](https://github.com/pytorch/test-infra)
#### [pytorch/pytorch-ci-hud](https://github.com/pytorch/pytorch-ci-hud)

### Which commit is used in CI for your PR?

### CI failure tips on PR
