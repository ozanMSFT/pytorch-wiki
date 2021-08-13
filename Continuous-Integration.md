> **Warning** This page is under construction

This document discusses the Continuous Integration (CI) system for PyTorch. 

Currently PyTorch utilizes Github Action, CircleCI and Jenkins CI for various different CI build/test configurations. We will discuss these related contents in the following sections:

- [CI System Overview](#ci-system-overview)
  - [Github Actions](#github-actions)
  - [CircleCI](#circleci)
  - [Jenkins](#jenkins)
- [CI Matrix](#ci-matrix)
  - [Basic Configurations](#basic-configurations)
  - [Variances](#configuration-variances)
- [Entrypoint for CI](#ci-entry-point)
  - [Build System](#ci-build)
  - [Test System](#ci-test)
  - [Binary Builds](#ci-binary)
- [What is CI testing and When](#ci-what-and-when)
  - [CI workflow on PR](#ci-on-pr)
  - [CI workflow on master commits](#ci-on-master-commits)
- [Customize CI behavior on PR](#customize-ci-on-pr)
- [Other Topics](#other-topics)
  - [What is the base commit used in CI for your PR?](#which-commit-is-used-in-ci)
  - [CI failure tips on PR](#ci-failure-tips)

# CI System Overview

## GitHub Actions

## CircleCI

See [this section on the "Running and writing tests" page](https://github.com/pytorch/pytorch/wiki/Running-and-writing-tests#using-github-label-to-control-ci-behavior-on-pr) for information on how to use GitHub labels to change what CircleCI jobs are run.