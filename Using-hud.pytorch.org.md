TLDR: Visit [hud.pytorch.org](https://hud.pytorch.org) for a quick glance into PyTorch's CI. Go to `hud.pytorch.org/pr/<pr number>` ([example](https://hud.pytorch.org/pr/65123)) or `hud.pytorch.org/commit/<long hash>` ([example](https://hud.pytorch.org/commit/ae00075ac71eb6ea81d05b12f153b61f215f870b)) for a detailed view of GitHub Actions jobs.

Please report any HUD bugs you find in our [issue tracker](https://github.com/pytorch/test-infra/issues)!


- [Introduction](#introduction)
- [HUD Home Page](#hud-home-page)
- [Pull Requests and Commits](#pull-requests-and-commits)

## Introduction

HUD is the PyTorch team's way of viewing results from CI runs. While Github provides built in CI job and workflow viewers, there are some limitations and extra features we wanted to add. Some examples of this include:
* All jobs on all workflows on a particular commit can be found on one page
* HUD log viewer loads the entire log withough truncating
* Log classifier can highlight what we think is the probable cause of failure for the job
* HUD main page shows all jobs on the `main` branch, making it easy to see consistent failures

While there are still limitations for HUD, we have more control over what and how things are displayed, so please feel free to submit feature requests [here](https://github.com/pytorch/test-infra/issues) for how we can make your PyTorch development easier.

## HUD Home Page

PyTorch's CI currently runs on GitHub Actions. It can be hard to tell at a glance how the various jobs across these services are doing on recent commits to PyTorch to determine if a failure on your pull request is a real failure vs. something that is broken on `main`. [hud.pytorch.org](https://hud.pytorch.org/) aims to fill this gap by providing a quick view over all the jobs on these commits.

![Screenshot 2023-06-23 at 11 04 24 AM](https://github.com/pytorch/pytorch/assets/44682903/f3e0d433-ed2f-4fc9-9ea6-065aea6afa7f)

1. Branches/Helpful Links: Quickly switch between different branches. Also has a link to MiniHUD, a more granular, commit based view.
2. Repo/Branch Switcher: Click on these to change the branch or repo. Only works on repos within Pytorch
3. Job Filter: Search for jobs in here and press "Go" to get the permalink
4. Grouped View Checkbox: Group the workflows by logical organization
5. HUD Commit View Link: Click on these commits to take you to the HUD commit viewing, which shows the jobs that ran
6. Github PR Link: Click on these to take you to the PR on Github

## Pull Requests and Commits

The page displays statuses for a commit, so if you are viewing a PR you will see statuses for the *latest* commit to that PR.

To find the HUD page for a PR, you can navigate to the page directly by going to `hud.pytorch.org/pr/<pr number>`, or by finding the link in the automated Dr. CI comment on your PR.

![Screenshot 2023-06-23 at 10 39 39 AM](https://github.com/pytorch/pytorch/assets/44682903/02a697c0-01da-4aee-aa9d-f43c3c106256)

For commits, you can go to `hud.pytorch.org/commit/<long commit hash>` or by clicking the link from the HUD main page.

You should be able to see the GitHub Actions jobs for that commit or PR. For PRs, the jobs shown are for the latest commit pushed to that PR. Failing and pending jobs are shown at the top of the page.

![Screenshot 2023-06-23 at 10 41 51 AM](https://github.com/pytorch/pytorch/assets/44682903/6531b050-146e-4062-8ef7-7961a19d908f)

**Logs**

For failing jobs, the line shown by the log viewer is our best guess at what is causing the job to fail.  This can be clicked to expand the log viewer and see the rest of the log, which is also searchable.

For all jobs, raw logs can be found for every job by clicking the `Raw Logs` link.  The Github log viewer can be accessed by clicking the job name.

**Artifacts**

Some jobs upload artifacts that can be found under `Expand to see Artifacts` at in at the bottom of each workflow box.  Artifacts generally include xml test reports and other build artifacts.  These can typically be found as soon as the job finishes, but some may take until the entire workflow finishes to show up due to limitations with Github Actions.
