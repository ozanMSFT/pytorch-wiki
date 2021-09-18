TLDR: Visit [hud.pytorch.org](https://hud.pytorch.org/build2/pytorch-master) for a quick glance into PyTorch's CI. Go to `hud.pytorch.org/pr/<pr number>` ([example](https://hud.pytorch.org/pr/65123)) or `hud.pytorch.org/commit/<long hash>` ([example](https://hud.pytorch.org/commit/ae00075ac71eb6ea81d05b12f153b61f215f870b)) for a detailed view of GitHub Actions jobs.

Please report any HUD bugs you find in our [issue tracker](https://github.com/pytorch/pytorch-ci-hud/issues)!

## Jobs

PyTorch's CI currently runs on 3 platforms, GitHub Actions, Jenkins, and CircleCI. It can be hard to tell at a glance how the various jobs across these services are doing on recent commits to PyTorch to determine if a failure on your pull request is a real failure vs. something that is broken on `master`. [hud.pytorch.org](https://hud.pytorch.org/build2/pytorch-master) aims to fill this gap by providing a quick view over all the jobs on these commits.

![image-20210917160028974](https://user-images.githubusercontent.com/9407960/133862655-acdb1278-e3a5-48c7-9fbe-8bad3b24ea6f.png)

1. You can pick a branch (`master`, `nightly`, `release/1.9`) to view commits for at the top of the page
2. There are many jobs that run for each commit, too many to show individually. Related jobs are instead grouped together by default. Group jobs can be identified by the bold icon for their status and the arrow icon at the end of their names in the header. Uncheck this box to disable grouping entirely.
3. These mark that a job is a grouped job (a bold icon or arrow). Click either one to expand the group and see the individual statuses of the jobs within.
4. The PR on GitHub related to this commit
5. The HUD page for this commit
6. The GitHub page for this commit
7. Filter job names by this regex

## Individual Pull Requests and Commits

GitHub provides a view for GitHub Action job logs for commits and PRs, but we received many reports that this was lacking in functionality and usability, so we created our own view which is also hosted on hud.pytorch.org. The page displays statuses for a commit, so if you are viewing a PR you will see statuses for the *latest* commit to that PR.

### Finding the Page

For PRs, you can navigate to the page directly by going to `hud.pytorch.org/pr/<pr number>`, or by finding the link in the automated Dr. CI facebook-github-bot comment on your PR.

![image-20210917154031914](https://user-images.githubusercontent.com/9407960/133863472-ccc19e93-2e94-40b5-8079-836b69a5774b.png)

For commits, you can go to `hud.pytorch.org/commit/<long commit hash>` or by clicking the link from the main HUD page (see "Jobs" above).

### Usage

You will need to sign in with GitHub on your first visit to the page. This is necessary so the HUD can make calls to the GitHub API.

![image-20210917154355041](https://user-images.githubusercontent.com/9407960/133863454-af2c0e43-b052-4660-9595-f18b653a5b32.png)

Once done, you should be able to see the GitHub Actions jobs for that commit or PR. For PRs, the jobs shown are for the latest commit pushed to that PR. The jobs are sorted so failing jobs are at the top. Some jobs that are not very helpful are grouped together at the bottom (such as the "Triage" jobs).

![image-20210917155138204](https://user-images.githubusercontent.com/9407960/133862719-715a414d-4160-490e-9fba-f12d73da55de.png)

1. If the doc builds for this commit or PR have finished, the link to the C++ / Python previews will be shown at the top.
2. You can view logs for this job by clicking the rightward arrow.
3. Some jobs report test results. The HUD can download and render these, similar to the test view on CircleCI. Click the button to expand and see failed tests. You can also see details of which tests ran in the "Summary" section.
4. Each job reports a status, one of:
   1. Success
   2. Failed
   3. Cancelled
   4. Skipped

#### Artifacts

Some jobs upload artifacts. Test report artifacts are hidden from view since their data is exposed via the test report renderer you see when you click the blue "Tests" button next to a test job. Other artifacts, some of which are stored in GitHub's artifact store and some in AWS S3, are shown below the job if there are any.

![image-20210917155828260](https://user-images.githubusercontent.com/9407960/133862734-2e2b3ad7-c44f-4e94-8bb6-2e22f7e2228b.png)

#### Logs

Logs are shown using VScode, so you can look through them with the same tools you would if you had downloaded them locally. The text is editable if you wish to operate on the logs, but these edits are not saved anywhere. You can also bring up the VSCode command palatte with F1 and run most commands from there. The "Log Level" selector enables or disables line filtering of [known-noisy lines](https://github.com/pytorch/pytorch-ci-hud/blob/e233c7d4de2701811d657c9d8b2cd612c018d123/src/PrDisplay.js#L374). Usually you will not have to move it off of "Minimal".


![image-20210917155410840](https://user-images.githubusercontent.com/9407960/133862614-ce586f18-b4a8-4746-85e2-dc4b4fe05aa3.png)

GitHub's log viewer supports sigils to mark the start and end of groups of lines, `##[group]` and `##[endgroup]` respectively. The log viewer here detects and automatically folds these. You can expand them by clicking the arrow on the left or F1 -> "Unfold All".

