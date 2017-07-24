NCCL
```
git remote add -f -t master --no-tags nccl https://github.com/nvidia/nccl
git fetch nccl
git checkout nccl/master
git subtree merge -P torch/lib/nccl nccl/master -m "Merge commit '`git rev-parse temporary-split-branch`'"
```


Gloo
```
git remote add -f -t master --no-tags gloo https://github.com/facebookincubator/gloo/
git fetch gloo
git subtree merge -P torch/lib/gloo gloo/master -m "Merge commit '`git rev-parse temporary-split-branch`'"
```