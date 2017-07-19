1. locally checkout the PR.
2. Then push TH/THC/THNN/THCUNN changes to upstream subtrees
3. remove those changes from local commit
4. sync from upstream


## 1. Locally checkout the PR

## 2. Then push TH/THC/THNN/THCUNN changes to upstream subtrees

### 2.1 Push TH Changes

```bash
git format-patch HEAD^ torch/lib/TH --stdout >tmp.patch
rm -rf /tmp/torch7
git clone https://github.com/torch/torch7 /tmp/torch7
mv tmp.patch /tmp/torch7
pushd /tmp/torch7
git am -p 2 tmp.patch
git push
popd
```

### 2.2 Push THC Changes

```bash
git format-patch HEAD^ torch/lib/THC --stdout >tmp.patch
rm -rf /tmp/cutorch
git clone https://github.com/torch/cutorch /tmp/cutorch
mv tmp.patch /tmp/cutorch
pushd /tmp/cutorch
git am -p 2 tmp.patch
git push
popd
```

### 2.3 Push THNN Changes

```bash
git format-patch HEAD^ torch/lib/THNN --stdout >tmp.patch
rm -rf /tmp/nn
git clone https://github.com/torch/nn /tmp/nn
mv tmp.patch /tmp/nn/
pushd /tmp/nn
git am -p 2 tmp.patch
git push
popd
```

### 2.4 Push THCUNN Changes

```bash
git format-patch HEAD^ torch/lib/THCUNN --stdout >tmp.patch
rm -rf /tmp/cunn
git clone https://github.com/torch/cunn /tmp/cunn
mv tmp.patch /tmp/cunn/
pushd /tmp/cunn
git am -p 2 tmp.patch
git push
popd
```

### 2.4 Push ATen Changes

```bash
git format-patch HEAD^ torch/lib/ATen --stdout >tmp.patch
rm -rf /tmp/ATen
git clone https://github.com/zdevito/ATen /tmp/ATen
mv tmp.patch /tmp/ATen/
pushd /tmp/ATen
perl -pi -w -e 's/torch\/lib/src/g;' tmp.patch
git am tmp.patch
git push
popd
```

## 3. remove those changes from local commit

```bash
git diff-tree --no-commit-id --name-only -r HEAD \
| grep "torch/lib/THNN/\|torch/lib/THCUNN/\|torch/lib/TH/\|torch/lib/THC/\/" \
| xargs -I {} bash -c "git checkout HEAD^ {} || git rm -f {}"

git commit --amend
```

4. sync from upstream
https://github.com/pytorch/pytorch/wiki/Updating-TH,-THC,-THNN,-THCUNN-subtrees