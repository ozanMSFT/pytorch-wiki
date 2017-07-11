Commands to update the subtrees

```bash
# select one of these
export THURL="https://github.com/torch/torch7"
export THREPO="TH"

export THURL="https://github.com/torch/cutorch"
export THREPO="THC"

export THURL="https://github.com/torch/nn"
export THREPO="THNN"

export THURL="https://github.com/torch/cunn"
export THREPO="THCUNN"

export THURL="https://github.com/zdevito/ATen"
export THREPO="ATen"
```

## Do this the first time:
```bash
git branch -D temporary-split-branch
git rm -rf torch/lib/$THREPO && git commit -m "removing $THREPO subtree"
git remote add -f -t master --no-tags $THREPO $THURL
git checkout $THREPO/master
git subtree split -P lib/$THREPO -b temporary-split-branch
git checkout master
git subtree add -P torch/lib/$THREPO temporary-split-branch
git branch -D temporary-split-branch
```

## In future, you can merge in additional changes as follows:
```bash

export GBRANCH="$(git rev-parse --abbrev-ref HEAD)"
export THREPO="TH"
git branch -D temporary-split-branch
git fetch    $THREPO
git checkout $THREPO/master
git subtree split -P lib/$THREPO -b temporary-split-branch
git checkout $GBRANCH
git subtree merge -P torch/lib/$THREPO temporary-split-branch -m "Merge commit '`git rev-parse temporary-split-branch`'"
unset GBRANCH

export GBRANCH="$(git rev-parse --abbrev-ref HEAD)"
export THREPO="THC"
git branch -D temporary-split-branch
git fetch    $THREPO
git checkout $THREPO/master
git subtree split -P lib/$THREPO -b temporary-split-branch
git checkout $GBRANCH
git subtree merge -P torch/lib/$THREPO temporary-split-branch -m "Merge commit '`git rev-parse temporary-split-branch`'"
unset GBRANCH

export GBRANCH="$(git rev-parse --abbrev-ref HEAD)"
export THREPO="THNN"
git branch -D temporary-split-branch
git fetch    $THREPO
git checkout $THREPO/master
git subtree split -P lib/$THREPO -b temporary-split-branch
git checkout $GBRANCH
git subtree merge -P torch/lib/$THREPO temporary-split-branch -m "Merge commit '`git rev-parse temporary-split-branch`'"
unset GBRANCH

export GBRANCH="$(git rev-parse --abbrev-ref HEAD)"
export THREPO="THCUNN"
git branch -D temporary-split-branch
git fetch    $THREPO
git checkout $THREPO/master
git subtree split -P lib/$THREPO -b temporary-split-branch
git checkout $GBRANCH
git subtree merge -P torch/lib/$THREPO temporary-split-branch -m "Merge commit '`git rev-parse temporary-split-branch`'"
unset GBRANCH
```

## Cleanup
```bash
git branch -D temporary-split-branch
unset THREPO
unset THURL
```