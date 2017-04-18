```
git remote add -f -t master --no-tags nccl https://github.com/nvidia/nccl
git fetch nccl
git checkout nccl/master
git subtree merge -P torch/lib/nccl nccl/master -m "Merge commit '`git rev-parse temporary-split-branch`'"
```