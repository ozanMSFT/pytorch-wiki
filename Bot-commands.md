## Commands available for everyone

```
@pytorchbot retest this please
```

This will trigger a rerun of failed tests on a PR. (WARNING: Currently this triggers rerun of ALL Jenkins tests, not just failed ones.)

```
@pytorchbot rebase this please
```

Requests pytorchbot to come and merge the PR with master. This causes tests to rerun, which is helpful if you need to take a fix from master.  **WARNING: This does NOT work with ghstack diffs. Rebase those locally.**

```
@pytorchbot label this please
```

Requests pytorchbot to look at a PR and apply labels according to what files were modified. (This happens automatically when a pull request is created; you might run this command if this failed for some reason.)

## Commands available to maintainers only

```
@pytorchbot merge this please
```

Can be used by maintainers to request immediate merge (i.e., you have verified CI is passing and the diff is approved)