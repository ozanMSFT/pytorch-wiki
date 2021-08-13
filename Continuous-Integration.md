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
- [Customize CI behavior on PR](#customize-ci-behavior-on-pr)
- [Other Topics](#other-topics)
  - [CI Internals](#ci-internals)
  - [Binary Builds](#binary-builds)
  - [Docker Builds](#docker-builds)
  - [Other Related Repos](#other-related-repos)
  - [Which commit is used in CI for your PR?](#which-commit-is-used-in-ci-for-your-pr)
  - [CI failure tips on PR](#ci-failure-tips-on-pr)


## CI System Overview

### Github Actions

### CircleCI

### Jenkins

## CI Matrix

### Basic Configurations

### Other Variances

## Entrypoint for CI

### Build System

### Test System

See [this section on the "Running and writing tests" page](https://github.com/pytorch/pytorch/wiki/Running-and-writing-tests#using-github-label-to-control-ci-behavior-on-pr) for information on how to use GitHub labels to change what CircleCI jobs are run.

## What is CI testing and When

### CI workflow on PR

### CI workflow on master commits

## Customize CI behavior on PR

## Other Topics

### CI Internals

### Binary Builds

### Docker Builds

### Other Related Repos

### Which commit is used in CI for your PR?

### CI failure tips on PR
