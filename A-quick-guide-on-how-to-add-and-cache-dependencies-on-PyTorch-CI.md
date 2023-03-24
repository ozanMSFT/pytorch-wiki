### Context
With the high volume of pull requests to PyTorch and the extensive scope of PyTorch test suite, PyTorch CI runs thousand of build and test jobs daily across multiple platforms including Linux (CPU, CUDA, ROCm), Windows (CPU, CUDA), MacOS (x86_64, M1), Android, and iOS.  This makes stability the most important of the CI.  Because stability includes many areas and means many things, we will focus only on CI dependencies in this wiki.  Specifically, this is about how to add and cache dependencies safely and reliably.

Most softwares are built on top of others and requires their dependencies to be in place to work.  PyTorch CI is no difference.  Over the years, some of the most common issues about setting up dependencies are:

* A dependency is not pinned.  It's updated upstream and unexpectedly break stuffs.
* A dependency is not cached and is setup from scratch every time a job requiring it is run.  This usually means downloading and installing something, i.e. a Conda package, from somewhere, i.e. the Internet.  Doing this multiple time and it's bound to fail flakily in some cases.

Like doing a science experiment, the solutions would be 1) to run the CI jobs in a control manner with fixed parameters and reproducible results and 2) to have all the dependencies close at hand.  They are achieved by:

1. Pin all CI dependencies to specific versions and update them explicitly.
1. Put all Linux dependencies into Docker images.
1. Put all Windows dependencies into [Windows AMI](https://github.com/pytorch/test-infra/tree/main/aws/ami/windows) used to launch Windows runners.
1. Put all MacOS dependencies into [GitHub cache](https://docs.github.com/en/actions/using-workflows/caching-dependencies-to-speed-up-workflows).

Adding a new CI dependency would most likely be done in both Linux, Windows, and MacOS.  So, let's go over the details for each platform.

### Linux dependencies
Common Linux CI pip dependencies are specify in Docker [requirements-ci.txt](https://github.com/pytorch/pytorch/blob/master/.ci/docker/requirements-ci.txt) and are installed for selected Python in [install_conda.sh](https://github.com/pytorch/pytorch/blob/master/.ci/docker/common/install_conda.sh#L90).  Pinning the version is recommended, i.e. `lintrunner==0.10.7`, unless there is a compelling reason to not do so.  Adding a dependency here will make it available to all Linux build and test jobs (easy peasy).

For specific cases, it's also ok to install conda and pip dependencies as part of Docker build script.  For example, ONNX dependencies are install in [install_onnx.sh](https://github.com/pytorch/pytorch/blob/master/.ci/docker/common/install_onnx.sh) script, which are available only in `pytorch-linux-focal-py3-clang10-onnx` Docker image to run ONNX tests.  The list of Docker images and what they have could be found in Docker [build.sh](https://github.com/pytorch/pytorch/blob/master/.ci/docker/build.sh) script.  [#96590](https://github.com/pytorch/pytorch/pull/96590) is a good example on how this could be done.  The process is roughly as follows:

1. Prepare what dependencies need to setup, i.e. [install_onnx.sh](https://github.com/pytorch/pytorch/blob/master/.ci/docker/common/install_onnx.sh)
1. Identify the Dockerfile to update.  There are currently three flavors:
    1. [Ubuntu](https://github.com/pytorch/pytorch/blob/master/.ci/docker/ubuntu/Dockerfile)
    1. [Ubuntu with CUDA](https://github.com/pytorch/pytorch/blob/master/.ci/docker/ubuntu-cuda/Dockerfile)
    1. [Ubuntu with ROCm](https://github.com/pytorch/pytorch/blob/master/.ci/docker/ubuntu-rocm/Dockerfile)
1. Add the script to the Dockerfile(s).  When it runs, it will be built as a new Docker layer, i.e.
```
ARG ONNX
# Install ONNX dependencies
COPY ./common/install_onnx.sh ./common/common_utils.sh ./
RUN if [ -n "${ONNX}" ]; then bash ./install_onnx.sh; fi
RUN rm install_onnx.sh common_utils.sh
```

Going beyond conda and pip dependencies, we could also put models frequently used by PyTorch CI in the Docker image to avoid hitting external hosts like https://huggingface.co excessively.  To go a bit into the details here, we host Docker images used by the CI on [AWS ECR](https://aws.amazon.com/ecr/) where the majority of our EC2 runners reside.  So getting Docker images with cached models from ECR is both cheaper, faster, and more reliable than getting them from external sources.  Fortunately, it's relatively easy to cache all these models by just getting them once when building the image, i.e. [#96793](https://github.com/pytorch/pytorch/pull/96793).  They will be readily available in the cache directory for later use.

### Windows dependencies
At the moment, there is no Docker support on Windows, so getting Windows dependencies ready is still a cumbersome process.  The exact steps of the current process is documented here.  Please reach out to PyTorch Dev Infra team if you need support during the process.

1. Windows AMI definition is in [test-infra](https://github.com/pytorch/test-infra/blob/main/aws/ami/windows/windows.pkr.hcl) repo using [Packer](https://developer.hashicorp.com/packer).  So, the first step is to install [Packer](https://developer.hashicorp.com/packer/downloads) on your dev box.
1. Get familiar with PowerShell on Windows.  You can also take a look at some [existing scripts](https://github.com/pytorch/test-infra/tree/main/aws/ami/windows/scripts/Installers).
1. Follow [the instruction](https://github.com/pytorch/test-infra/tree/main/aws/ami/windows) to build and publish a new AMI `packer build -var 'skip_create_ami=false' .`.
    1. Only PyTorch Dev Infra team with access to our AWS account could publish a new AMI
    1. There is only one Windows AMI shared between CPU and CUDA jobs.  This is a known pain point that has yet been addressed.
1. Depending on the types of dependencies, there are:
    1. [Install-Conda-Dependencies.ps1](https://github.com/pytorch/test-infra/blob/main/aws/ami/windows/scripts/Installers/Install-Conda-Dependencies.ps1) to install all Conda dependencies.  Again, pin the version whenever you can.  Questions would be asked otherwise.
    1. [Install-Pip-Dependencies.ps1](https://github.com/pytorch/test-infra/blob/main/aws/ami/windows/scripts/Installers/Install-Pip-Dependencies.ps1) to install all pip dependencies.
    1. Other dependencies are covered in their own PowerShells scripts, i.e. [Windows CUDA](https://github.com/pytorch/test-infra/blob/main/aws/ami/windows/scripts/Installers/Install-CUDA-Tools.ps1)

Once the build successes and a new AMI is published.  It's time to test it before rolling it out to production.  This is done on [pytorch-canary](https://github.com/pytorch/pytorch-canary)

1. Update the Windows AMI used by `pytorch-canary` [here](https://github.com/fairinternal/pytorch-gha-infra/blob/main/runners/canary.tf#L78)
1. Once merged, submit a pull request to `pytorch-canary`, i.e. [#158](https://github.com/pytorch/pytorch-canary/pull/158), to run Windows build and test jobs.
    1. The first thing is to confirm that the new AMI is used. This information could be found in the `Setup Windows` step in PyTorch CI where it shows the AMI ID.
    1. Ensure that all Windows jobs including binaries jobs pass.
1. Finally, update the Windows AMI used by `pytorch` [here](https://github.com/fairinternal/pytorch-gha-infra/blob/main/runners/main.tf#L89)

### MacOS dependencies
Both Docker and AMI options are not available for MacOS at the moment, so its dependencies couldn't be setup beforehand and they are still downloaded when the CI jobs run.  Nevertheless, we use [GitHub cache](https://docs.github.com/en/actions/using-workflows/caching-dependencies-to-speed-up-workflows) to cache all Conda and pip dependencies.  Adding a new dependency could then be done in a straightforward way by putting it into:

1. Pinned Conda dependencies are in `.github/requirements/conda-env-OS-ARCH.txt` Conda environment files.  They are:
    1. [conda-env-macOS-ARM64.txt](https://github.com/pytorch/pytorch/blob/master/.github/requirements/conda-env-macOS-ARM64) (MacOS M1)
    1. [conda-env-macOS-X64.txt](https://github.com/pytorch/pytorch/blob/master/.github/requirements/conda-env-macOS-X64) (MacOS x86_64)
    1. [conda-env-iOS.txt](https://github.com/pytorch/pytorch/blob/master/.github/requirements/conda-env-iOS) (iOS)
1. Pinned pip dependencies are in `.github/requirements/pip-requirements-OS.txt` pip requirements files.  They are:
    1. [pip-requirements-macOS.txt](https://github.com/pytorch/pytorch/blob/master/.github/requirements/pip-requirements-macOS.txt) (MacOS M1 and x86_64)
    1. [pip-requirements-iOS.txt](https://github.com/pytorch/pytorch/blob/master/.github/requirements/pip-requirements-iOS.txt) (iOS)

Similarly, a new Conda env or pip requirements file could be added for other platforms.  The recommended way is to pass the file to [setup-miniconda](https://github.com/pytorch/test-infra/tree/main/.github/actions/setup-miniconda) and let the action download, install, and cache all the dependencies automatically, i.e. [_mac-test.yml](https://github.com/pytorch/pytorch/blob/master/.github/workflows/_mac-test.yml#L109-L123) workflow:

```
- name: Setup miniconda (x86, py3.8)
  if: ${{ runner.arch == 'X64' }}
  uses: pytorch/test-infra/.github/actions/setup-miniconda@main
  with:
    python-version: 3.8
    environment-file: .github/requirements/conda-env-${{ runner.os }}-${{ runner.arch }}
    pip-requirements-file: .github/requirements/pip-requirements-${{ runner.os }}.txt

- name: Setup miniconda (arm64, py3.9)
  if: ${{ runner.arch == 'ARM64' }}
  uses: pytorch/test-infra/.github/actions/setup-miniconda@main
  with:
    python-version: 3.9
    environment-file: .github/requirements/conda-env-${{ runner.os }}-${{ runner.arch }}
    pip-requirements-file: .github/requirements/pip-requirements-${{ runner.os }}.txt
```
