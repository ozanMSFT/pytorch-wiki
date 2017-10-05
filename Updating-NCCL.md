```bash
export THREPO="nccl"
export THURL="https://github.com/nvidia/nccl"
git remote add -f -t master --no-tags $THREPO $THURL
git fetch    $THREPO
git subtree merge -P torch/lib/$THREPO nccl/master -m "Merge commit '`git rev-parse nccl/master`'"
```