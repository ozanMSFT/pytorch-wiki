Author: garymiguel. Created: 2021-07-16

## Pull request review protocol

Reviewers and authors should follow [Google's Code Review Developer Guide](https://google.github.io/eng-practices/review/).

Authors and reviewers should feel free to discuss things synchronously (in-person or Teams calls) to avoid a lot of back-and-forth.

### Authors

PRs that are not ready for review should be marked as Draft so that the on-duty doesn't see them.

There are two options for an author to get a pull request reviewed:

1. Assign reviewer(s) of their choice in the GitHub UI, or
2. Wait for the person [on duty](#Duty-rotation) to assign themselves.

Some good reasons an author may assign reviewers:

- That reviewer has much more context on the change than anybody else on the team.
- That reviewer is the only person with expertise in the code being changed.

If this PR is for an ADO Product Backlog Item and work on that item cannot proceed until it's merged, the author should move the item into the "In Review" column on the Kanban.

### Reviewers

All team members should check in **daily** on all PRs that are assigned to them via https://github.com/pulls/assigned, notifications filtered by [reason:assign is:unread](https://github.com/notifications?query=reason%3Aassign+is%3Aunread), or email notifications (whichever UI they prefer).

If unable to review a ready-for-review PR on a given workday, assignees should comment on the PR saying when they expect to review it.

If an assigned reviewer thinks someone else would be a much better reviewer, or is needed to review a specific part of the PR, they should:

1. Review the PR as best they can. This will help the assigned reviewer learn about a new area.
2. Add a comment suggesting a different reviewer who can finish the review and approve and merge the change.

If the author does not work at Microsoft and has not responded to comments in over a week, add the label "onnx-needs-info", and un-assign yourself.

## Duty rotation

### Mechanics

1-day shifts, rotating Monday – Friday at 00:00 UTC (Monday-Friday 08:00 Beijing, Sunday-Thursday 16:00 or 17:00 Pacific time).

The rotation will include all SDEs working full time on the PyTorch exporter. New team members will be added to the rotation when they're ready.

### Rotation duties

For the duration of their duty shift, the on-duty person should devote their full working day to the following tasks, in the following priority:

- Attend required meetings.
- Review previously assigned pull requests.
- Review new pull requests.
- GitHub issue triage.
- Review `onnx-needs-info` items.

The on-duty person should not expect to have time to do anything else (e.g., write code). And they are not expected to finish triaging all issues or `onnx-needs-info` items, especially at first since we have a long backlog of untriaged items. They should finish as much as they can in their normal workday.

### Review new pull requests

- Use the queries below to find unassigned PRs.
  - [onnx in title](https://github.com/pytorch/pytorch/pulls?q=is%3Apr+is%3Aopen+draft%3Afalse+no%3Aassignee+-label%3A%22onnx-needs-info%22+onnx+in%3Atitle+NOT+%5BWIP%5D+in%3Atitle)
  - [module: onnx](https://github.com/pytorch/pytorch/pulls?q=is%3Apr+is%3Aopen+draft%3Afalse+no%3Aassignee+-label%3A%22onnx-needs-info%22+label%3A%22module%3A+onnx%22)

- For each PR...
- Look over the PR briefly.
- If you feel competent to review it at least partially, assign yourself in the GitHub UI and review the PR.
- If you don't feel competent to review it entirely, add a comment explaining what parts you think should be reviewed by someone else. If you know who else should review it, assign that person in the GitHub UI.
- If the PR is against master and has been approved by someone on the PyTorch exporter team, ask someone from Facebook to import it. TODO: figure out the GitHub alias to mention.
- Check appendix for details on setting proper PR descriptions when merging.

### Review previously assigned pull requests

Continue any previously assigned pull request reviews according to [protocol](#Pull-request-review-protocol).

### GitHub issue triage

#### Triage new issues

- Use the queries below to find issues that need to be triaged.

  - [no assignee](https://github.com/pytorch/pytorch/issues?q=is%3Aissue+is%3Aopen+label%3A%22module%3A+onnx%22+-label%3Aonnx-triaged+-label%3Aonnx-needs-info+no%3Aassignee)

  - [assignee:@me](https://github.com/pytorch/pytorch/issues?q=is%3Aissue+is%3Aopen+label%3A%22module%3A+onnx%22+-label%3Aonnx-triaged+-label%3Aonnx-needs-info+assignee%3A%40me)

  - [onnxruntime](https://github.com/microsoft/onnxruntime/issues?q=is%3Aissue+is%3Aopen+label%3Acomponent%3Aconverter-pytorch+-label%3Astatus%3Amore-info-needed)

- For each issue...

  - Spend a reasonable amount of time (< 1 hour) to try to understand and reproduce the issue.
  - If possible, edit the title and/or body for clarity, to add repro instructions, etc.
  - If unable to understand or reproduce:
    - Add a comment, @mentioning the reporter and explain that you can't understand or reproduce.
    - Add the label "onnx-needs-info" (for issues in onnxruntime repo, use "status:more-info-needed").
    - Move on to the next issue.

  - If able to understand and reproduce...

    - Add the label "onnx-triaged"
    - If it seems like it will take less than 1 hour to resolve, then attempt to resolve it immediately. See [resolving an issue](bookmark://_Resolving_an_issue).
    - If it seems like it will (or actually does) take more than 1 hour to resolve, don't do anything else.

#### Resolving an issue

If it can be resolved without changes to the PyTorch source code (e.g., it was just a question, or the reporter can change their code), then:

- Add a comment answering the question or explaining how the reporter can change their code. Mention the reporter (using `"@<user name>"`).
- Add the label `onnx-needs-info`.

If changes to the PyTorch source code are required, in the message of the commit that fixes the issue write ["fixes #<issue id>"](https://docs.github.com/en/issues/tracking-your-work-with-issues/linking-a-pull-request-to-an-issue). Once this commit is in the master branch, the GitHub issue should automatically be closed.

### Review "onnx-needs-info" items

- Use [this query](https://github.com/pytorch/pytorch/issues?q=is%3Aopen+label%3Aonnx-needs-info+sort%3Aupdated-desc) and go through each item.
- If there's been a response from the author / reporter, remove the "onnx-needs-info" label and assign the issue / PR to the person who previously added that label.
- If there's been no response in 2 months, close it with the comment:
  - It's been over 2 months, so closing this. Feel free to open another one.