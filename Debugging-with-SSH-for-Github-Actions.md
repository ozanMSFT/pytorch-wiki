# Debug using `with-ssh` for Github Actions

## Context

`with-ssh` is a feature for Github Actions that was created to replicate the features set of CircleCI’s [*Re-run with SSH*](https://circleci.com/docs/2.0/ssh-access-jobs/) feature that a lot of the development team is used to.

## Platform availability:

- [x] Linux (https://github.com/pytorch/pytorch/pull/62280)
- [x] Windows (https://github.com/pytorch/pytorch/pull/63440)

## Comparisons to CircleCI’s Re-run with SSH

### Similarities

* 2 hour time limit after job has finished for when SSH sessions will be closed out and runner will return to the regular runner pool
* Public keys for ssh are pulled from Github using `https://github.com/${github.actor}.keys`
    * Example: https://github.com/seemethere.keys

### Differences

* Only works for FB employees on the FB VPN
* Requires a label (*with-ssh* in this case) to be applied to the Pull Request
    * As opposed to clicking *Re-run with SSH* through the CircleCI UI
    * **NOTE:** SSH keys will not be added to jobs ran *before* the label is applied so workflows will need to be re-ran *after* the *with-ssh* label has been applied
        * This is true even if the job is re-run using GitHub’s “re-run jobs” button
        * **A completely new workflow run needs to be triggered after the label is applied**
* Only works for `pull_request` events and will not work on *main branch* `push` events

## Known limitations

* Only works for FB employees on FB VPN
    * No current planned support for outside collaborators yet

## Workflow for users

1. Label your PR with the *with-ssh* label
    1. <img width="387" alt="Screen Shot 2021-08-04 at 11 29 30 AM" src="https://user-images.githubusercontent.com/1700823/130687591-f0cc6528-b12c-45f8-895e-8c1701f419d9.png">
2. Push a new commit / re-run completed workflows, see below for re-running jobs through the Github UI
    1. ![image (1)](https://user-images.githubusercontent.com/1700823/130687616-eb899bd4-d5e3-441c-9134-6637c1b0857c.png)
3. Traverse to logs for a `build` or `test` job that runs the `add-github-ssh-key` step added (currently all of our linux workflows have this enabled)
    1. <img width="1254" alt="Screen Shot 2021-08-04 at 11 47 56 AM" src="https://user-images.githubusercontent.com/1700823/130687634-973fc0ac-558c-44eb-8568-eed10027062b.png">
4. Use the SSH command provided to log into the node:
    1. <img width="948" alt="Screen Shot 2021-08-04 at 11 49 25 AM" src="https://user-images.githubusercontent.com/1700823/130687653-b74ce62e-0e7b-40b0-9957-5d157a6b0c62.png">


## Notes for users

## General
* The default timeout for these jobs is **2 hours after workflows have completed**
  * Users will be kicked after workflows have either *timed out* or have been *cancelled*

## Windows
* The *Windows* workspace is currently located at `C:\actions-runner\_work\pytorch\pytorch\pytorch-${run_id}`
  * See https://github.com/pytorch/pytorch/pull/61932 for more information as to why this is necessary
* To use other shells for *Windows* just append the shell you'd like to run to your ssh command like:
  * `ssh runneruser@ec2-3-238-136-38.compute-1.amazonaws.com -- bash.exe`

## Planned features
- [ ] Add support for RDP tunneling using SSH on Windows
  - see https://www.ntkernel.com/securing-remote-desktop-with-ssh/


cc @ezyang @seemethere @malfet @walterddr @lg20987 @pytorch/pytorch-dev-infra