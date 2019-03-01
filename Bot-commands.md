## Commands available for everyone

```
@pytorchbot retest this please
```

This will trigger a rerun of failed tests on a PR. (WARNING: Currently this triggers rerun of ALL Jenkins tests, not just failed ones.)

```
@pytorchbot rebase this please
```

Requests pytorchbot to come and merge the PR with master. This causes tests to rerun, which is helpful if you need to take a fix from master. This can be used by anyone.

## Commands available to maintainers only

```
@pytorchbot merge this please
```

Can be used by maintainers to request immediate merge (i.e., you have verified CI is passing and the diff is approved)