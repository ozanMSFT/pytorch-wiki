# Docker image build on CircleCI

For those who used to work with the docker image build process, we’ve migrated docker image build job from Jenkins to CircleCI. 

## Current config


The config of docker image  now live at https://github.com/pytorch/pytorch/tree/master/.circleci/docker 
And we have weekly jobs to rebuild all the images:  https://github.com/pytorch/pytorch/blob/master/.circleci/config.yml#L5695-L5751  (this link might not work if we ever update config.yml file, so please search for “docker_build_job” to be sure)

and you can find existing images (both permanent and weekly) at [http://docker.pytorch.org](http://docker.pytorch.org/)  which will be updated hourly by this job: https://github.com/pytorch/pytorch/blob/master/.circleci/config.yml#L5752-L5776 (for same reason as above, you can search “ecr_gc_job” to be sure)


## Q & A

### How to trigger new build process if I don’t want to wait for a week?

remove https://github.com/pytorch/pytorch/blob/a54dc87e8ebf7634d3fb2f32dd32b73c1a4d095f/.circleci/verbatim-sources/workflows-docker-builder.yml#L2-L8 , so your PR can/will trigger build job. **REMEMBER** to run [regenerate.sh](https://github.com/pytorch/pytorch/blob/master/.circleci/regenerate.sh) when you’re inside .circle directory after you update the file 

### Where to find tags for newly created/pushed images?

[http://docker.pytorch.org](http://docker.pytorch.org/) which will be updated hourly

### How do we purge old images / what’s the retention policy?

We have ecr_gc_job job (you can search for it in config.yml) that runs every hour to purge old images. Currently, we need temporary images for 1 day, and weekly builds for 2 weeks. And we will keep image with tags defined in https://github.com/pytorch/pytorch/blob/master/.circleci/verbatim-sources/workflows-ecr-gc.yml#L13 forever. 
code for the purge job is https://github.com/pytorch/pytorch/blob/master/.circleci/ecr_gc_docker/gc.py 

### How to use new images?

1. locate new image tag at http://docker.pytorch.org/pytorch.html
2. test with new images, you need to update https://github.com/pytorch/pytorch/blob/master/.circleci/cimodel/data/pytorch_build_definitions.py#L16 (or search for DOCKER_IMAGE_VERSION) and regenerate config.yml
3. update  https://github.com/pytorch/pytorch/blob/master/.circleci/verbatim-sources/workflows-ecr-gc.yml#L13  and land changes to make sure the images won’t be purged

