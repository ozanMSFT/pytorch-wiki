## Fork & Clone PyTorch

Fork the Pytorch repository to your Github account and create a local clone.

1. Go to https://github.com/pytorch/pytorch
1. Press Fork on the top right.
1. When asked where to fork the repository, choose to fork it to your username.
1. Your fork will be created at https://github.com/<username>/pytorch.
1. Clone your GitHub fork (replace <username> with your username):
    ```
    git clone --recursive https://github.com/<username>/pytorch.git
    cd pytorch
    # if you are updating an existing checkout
    git submodule sync
    git submodule update --init --recursive
    ```
1. Add an `upstream` remote to stay in sync with the main repo. Always push changes to `origin` (your fork)!
    ```
    $ git remote add upstream https://github.com/pytorch/pytorch
    $ git remote -v
    > origin    https://github.com/<username>/pytorch.git (fetch)
    > origin    https://github.com/<username>/pytorch.git (push)
    > upstream  https://github.com/pytorch/pytorch.git (fetch)
    > upstream  https://github.com/pytorch/pytorch.git (push)
    ```

---

## Nightly Checkout & Pull for Python-only development
The `tools/nightly.py` script is provided to allow Python-only development of
PyTorch. Choose this if you aren't changing or compiling C++ code. 
This uses `conda` and `git` to check out the nightly development
version of PyTorch and installs pre-built binaries into the current repository.

You can use this script to check out a new nightly branch with the following:

```bash
cd pytorch
./tools/nightly.py checkout -b my-nightly-branch
conda activate pytorch-deps
```

Or if you would like to re-use an existing conda environment, you can pass in
the regular environment parameters (`--name` or `--prefix`):

```bash
./tools/nightly.py checkout -b my-nightly-branch -n my-env
conda activate my-env
```

You can also use this tool to pull the nightly commits into the current branch:

```bash
./tools/nightly.py pull -n my-env
conda activate my-env
```

Pulling will reinstall the PyTorch dependencies as well as the nightly binaries
into the repo directory.
