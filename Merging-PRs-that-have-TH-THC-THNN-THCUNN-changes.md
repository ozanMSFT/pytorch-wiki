locally checkout the PR.
Then do:

Push TH Changes

```bash
git format-patch HEAD^ torch/lib/TH --stdout >tmp.patch
rm -rf /tmp/torch7
git clone github.com/torch/torch7 /tmp/torch7
mv tmp.patch /tmp/torch7
pushd /tmp/torch7
git am -p 2 tmp.patch
git push
popd
```

Push THC Changes

```bash
git format-patch HEAD^ torch/lib/THC --stdout >tmp.patch
rm -rf /tmp/cutorch
git clone github.com/torch/cutorch /tmp/cutorch
mv tmp.patch /tmp/cutorch
pushd /tmp/cutorch
git am -p 2 tmp.patch
git push
popd
```

Push THNN Changes

```bash
git format-patch HEAD^ torch/lib/THNN --stdout >tmp.patch
rm -rf /tmp/nn
git clone github.com/torch/nn /tmp/nn
mv tmp.patch /tmp/nn/
pushd /tmp/nn
git am -p 2 tmp.patch
git push
popd
```

Push THCUNN Changes

```bash
git format-patch HEAD^ torch/lib/THCUNN --stdout >tmp.patch
rm -rf /tmp/cunn
git clone github.com/torch/cunn /tmp/cunn
mv tmp.patch /tmp/cunn/
pushd /tmp/cunn
git am -p 2 tmp.patch
git push
popd
```