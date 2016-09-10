Commands to update the subtrees

```bash
THURL="https://github.com/torch/cutorch"
THREPO="THC"
# Do this the first time:
git rm -rf torch/lib/$THREPO && git commit -m "removing $THREPO subtree"
git remote add -f -t master --no-tags $THREPO $THURL
git checkout $THREPO/master
git subtree split -P lib/$THREPO -b temporary-split-branch
git checkout master
git subtree add --squash -P torch/lib/$THREPO temporary-split-branch
git branch -D temporary-split-branch


# In future, you can merge in additional changes as follows:
git checkout $THREPO/master
git subtree split -P lib/$THREPO -b temporary-split-branch
git checkout master
git subtree merge --squash -P torch/lib/$THREPO temporary-split-branch
# Now fix any conflicts if you'd modified torch/lib/$THREPO.
git branch -D temporary-split-branch
```