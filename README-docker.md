# Centralised GitHub Workflows for Docker

<!-- ../okapi/doc/md2toc -l 2 -h 3 README-docker.md -->
* [Introduction](#introduction)
* [Usage](#usage)
* [Image metadata](#image-metadata)
* [Configuration](#configuration)

## Introduction

The Workflows in this repository named `docker*.yml` are for building and publishing Docker images to FOLIO Docker Hub.

These stand-alone Workflows are intended for back-end module repositories that only have a Dockerfile and no source-code. For example for [folio-keycloak](https://github.com/folio-org/folio-keycloak).

Other back-end repositories continue to use the Jenkins-based pipelines to build and publish their module artifacts and Docker images.

(Portions of these Workflows are also utilised by the GitHub Workflows for Go-based back-end modules.)

## Usage

Create a `.github/workflows` directory in the root of the module repository, and add a file named `do-docker.yml` with the following content.
The filename is not important -- it can be anything.

```yaml
name: Docker central workflow

on:
  push:
    branches: [ main, master ]
    workflow_dispatch:

jobs:
  docker:
    uses: folio-org/.github/.github/workflows/docker.yml@v1
    secrets: inherit
```

The Workflow will operate on merge to mainline.

When git tags are pushed for a release, then the docker image is published to the "folioorg" repository of FOLIO Docker Hub.

Otherwise the snapshot docker image is published to the "folioci" repository of FOLIO Docker Hub.\
For example: https://hub.docker.com/r/folioci/folio-keycloak

## Image metadata

The docker image will have various labels automatically applied.

Note: If the following label is empty, then that is because the module's GitHub repository is missing the "About" description in the top-right corner of its GitHub front page.
See advice at [Create a new FOLIO module and do initial setup](https://dev.folio.org/guidelines/create-new-repo/),
and bear in mind that Docker Hub imposes a [content length limit](https://github.com/peter-evans/dockerhub-description#content-limits) of 100 bytes for that short-description, so it will be truncated at that.

```
org.opencontainers.image.description=
```

## Configuration

There is only one configuration variable, which enables a single "documentation" label to be added to the docker image.
Add the following section at the end of the Workflow after the "secrets" item:

```yaml
...
    with:
      docker-label-documentation: 'https://.../documentation.md'
```
