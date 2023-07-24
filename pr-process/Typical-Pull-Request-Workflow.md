<!-- SOURCE: https://docs.google.com/document/d/1oNhUeGE-8ajsYaMpoV6ZQANQZVeKrdFanI9VMbFzOzc/edit -->
Once a new PR has been created, it goes through the following steps before being merged into the codebase.


[[/images/PR-workflow.png|link=https://docs.google.com/document/d/1oNhUeGE-8ajsYaMpoV6ZQANQZVeKrdFanI9VMbFzOzc/edit|The PyTorch Pull Request Workflow (click on the image for full details)]]

* _Green: Contributor responsibility_
* _Orange: PyTorch Maintainer responsibility_


### Triage
* Someone from the team will come and triage your PR by applying labels to it, checking it for validity, and assigning reviewers to it.
* This will happen within two USA business days. If it has been longer than that, then ping @zou3519. 

### Review
* PR-time tests will run on your PR (first-time contributors may need the reviewer to approve running the tests). These will give signals. If your PR fails any builds or tests, it is likely the reviewer will request changes.
* The reviewer(s) will check if the PR looks good. If additional changes are needed, the reviewers will request changes.
* It may take some time for the reviewer(s) to respond, depending on the reviewer and the part of the codebase. If it has been more than a couple of days (2-3+), please either ping them or the person who added the `triaged` label.

### Approval
* Once everything looks good to a reviewer, they will approve the PR. Only one approving review is needed to move onto the next step; you do not need approval from all the reviewers unless otherwise noted.

### Initiate merge
* Both you (the contributor) and the reviewer(s) are able to merge the PR. Once a reviewer has approved the PR, you may merge the PR unless otherwise noted.
* To merge a PR, comment `@pytorchbot merge` on the PR. See [here](https://github.com/pytorch/pytorch/wiki/Bot-commands) for a list of other bot commands.

### Trunk tests
* pytorchbot will initiate trunk tests. All tests (PR tests and trunk tests) are required to pass before merging. You can follow the progress of these in the GitHub UI; these generally take another 6-8 hours to complete.
* If any of the trunk tests fail, then the merge will abort:
    * If the failure is legitimate, please fix your PR and re-initiate a merge
    * If the failure seems unrelated, please ask the reviewers for next steps.
    * If you are unsure, please ask the reviewers for next steps.

### Merged
* pytorchbot will merge the PR. When the PR is merged, it will show up as “Closed” in the GitHub UI and pytorchbot will apply the “Merged” label.
* If this process fails, pytorchbot should give an actionable error message; if not, please ping the reviewer(s).
* At this point, there is no action required from the contributor unless there is a revert.

### Post-merge CI failures
* After the PR is merged, we will run additional integration tests with other libraries such as torchvision. The status of these tests are not visible in the pytorch/pytorch GitHub UI.
* If the PR fails these tests, then we may revert the PR.

### Dealing with reverts
* A bot will comment on the PR if it has been reverted.
* If this happens, then we recommend submitting a new PR with your changes and cc’ing the original reviewers.


