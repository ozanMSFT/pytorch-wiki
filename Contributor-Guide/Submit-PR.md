
### Tip: Skip CI 
* If a commit is simple and doesn't affect any code (keep in mind that some docstrings contain code
  that is used in tests), you can add `[skip ci]` (case sensitive) somewhere in your commit message to
  [skip all build / test steps](https://github.blog/changelog/2021-02-08-github-actions-skip-pull-request-and-push-workflows-with-skip-ci/).
  Note that changing the pull request body or title on GitHub itself has no effect.