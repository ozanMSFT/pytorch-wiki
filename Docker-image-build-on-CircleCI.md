For those who used to work with the docker image build process, we’ve migrated docker image build job from Jenkins to CircleCI. 

## Current config


The config of docker image  now live at https://github.com/pytorch/pytorch/tree/master/.circleci/docker 
And we have weekly jobs to rebuild all the images:  https://github.com/pytorch/pytorch/blob/master/.circleci/config.yml#L5695-L5751  (this link might not work if we ever update config.yml file, so please search for “docker_build_job” to be sure)

and you can find existing images (both permanent and weekly) at [http://docker.pytorch.org](http://docker.pytorch.org/)  which will be updated hourly by this job: https://github.com/pytorch/pytorch/blob/master/.circleci/config.yml#L5752-L5776 (for same reason as above, you can search “ecr_gc_job” to be sure)


## Q & A

### How to trigger new build process if I don’t want to wait for a week?

Comment out or remove https://github.com/pytorch/pytorch/blob/cb9029df9db397f66da234d21b5ddafd6be6e602/.circleci/generate_config_yml.py#L120-L127, so your PR can/will trigger build job. **REMEMBER** to run [regenerate.sh](https://github.com/pytorch/pytorch/blob/master/.circleci/regenerate.sh) when you’re inside .circle directory after you update the file.

### Where to find tags for newly created/pushed images?

[http://docker.pytorch.org](http://docker.pytorch.org/) which will be updated hourly

### How do we purge old images / what’s the retention policy?

We have ecr_gc_job job (you can search for it in config.yml) that runs every hour to purge old images. Currently, we need temporary images for 1 day, and weekly builds for 2 weeks. And we will keep image with tags defined in https://github.com/pytorch/pytorch/blob/master/.circleci/verbatim-sources/workflows-ecr-gc.yml#L13 forever. 
code for the purge job is https://github.com/pytorch/pytorch/blob/master/.circleci/ecr_gc_docker/gc.py 

### How to use new images?

1. locate new image tag at http://docker.pytorch.org/pytorch.html
2. test with new images, you need to update https://github.com/pytorch/pytorch/blob/master/.circleci/cimodel/data/pytorch_build_definitions.py#L16 (or search for DOCKER_IMAGE_VERSION) and regenerate config.yml
3. update  https://github.com/pytorch/pytorch/blob/master/.circleci/verbatim-sources/workflows-ecr-gc.yml#L13  and land changes to make sure the images won’t be purged

### The production images disappeared, what should I do?

Sometimes there is a bug in ecr_gc_job and it deletes Docker images it shouldn't. All Docker images are also saved to S3 with a one month retention period, so there's a chance they may still be there.  You can use this script to recover in that case:

```
import yaml
import boto3
import subprocess
import os
import requests
import argparse
import multiprocessing

parser = argparse.ArgumentParser(description='Recover Docker images from S3 to ECR')
parser.add_argument('tag', metavar='TAG', help='tag to recover, something like 07597f23-fa81-474c-8bef-5c8a91b50595')
parser.add_argument('-j', metavar='N', type=int, default=8, help='number of jobs to run in parallel')
args = parser.parse_args()
recover_id = args.tag

r = yaml.safe_load(requests.get('https://raw.githubusercontent.com/pytorch/pytorch/master/.circleci/config.yml').text)
builds = [b['docker_build_job']['image_name'] for b in r['workflows']['docker_build']['jobs'] if isinstance(b, dict)]

def image_name(b):
    return "{}:{}".format(b, recover_id)

def s3_url(b):
    return "pytorch/base/{}.tar".format(image_name(b))

def upload_to_ecr(b):
    print(b)
    s3 = boto3.client('s3')
    print("s3 url: {}".format(s3_url(b)))
    tmp_file = '/tmp/{}.tar'.format(image_name(b))
    s3.download_file('ossci-linux-build', s3_url(b), tmp_file)
    print("loading...")
    subprocess.check_call(('docker', 'load', '--input', tmp_file))
    print("pushing...")
    subprocess.check_call(('docker', 'push', '308535385114.dkr.ecr.us-east-1.amazonaws.com/pytorch/{}'.format(image_name(b))))
    print("done.")
    print()

pool = multiprocessing.Pool(processes=args.j)
pool.map(upload_to_ecr, builds)
```

### How to add a new base docker image? 
Note this instruction provides guidance to add a new **base** docker image. If you could reuse one of available docker images listed in https://github.com/pytorch/pytorch/blob/master/.circleci/docker/build.sh#L37, please do so and not adding new ones.

- Add an entry in https://github.com/pytorch/pytorch/blob/master/.circleci/docker/build.sh#L37 and make changes to Dockerfiles accordingly. 
- Test your image by building it locally.
- Add a repo in AWS ECR to hold your image. It requires access to PyTorch's AWS account to do this step.
- Trigger a new build process as described above.
- Remove https://github.com/pytorch/pytorch/blob/master/.circleci/verbatim-sources/workflows-ecr-gc.yml#L2-L8 so that docker_hub_index_job can be run on your PR. http://docker.pytorch.org/ will then display images pushed into the newly created repo.
- **WAIT UNTIL ALL BUILD JOBS TO FINISH** and make sure all new images have been uploaded. Save the tag of the new images.
- Run regenerated.sh with the new tag and update your PR.

See an example PR https://github.com/pytorch/pytorch/pull/36187