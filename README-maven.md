# Centralised GitHub Workflows for Maven

<!-- ../okapi/doc/md2toc -l 2 -h 3 README-maven.md -->
* [Introduction](#introduction)
* [Usage](#usage)
* [Configuration](#configuration)
    * [Configuration: java-version](#configuration-java-version)
    * [Configuration: publish-module-descriptor](#configuration-publish-module-descriptor)
    * [Configuration: allow-snapshots-release](#configuration-allow-snapshots-release)
    * [Configuration: do-sonar-scan](#configuration-do-sonar-scan)
    * [Configuration: do-docker](#configuration-do-docker)
    * [Configuration: docker-health-command](#configuration-docker-health-command)
    * [Configuration: docker-label-documentation](#configuration-docker-label-documentation)
* [Limitations](#limitations)
    * [Only top-level Dockerfile](#only-top-level-dockerfile)
* [Docker image metadata](#docker-image-metadata)

## Introduction

> [!CAUTION]
> NOTE: In-development [FOLIO-4126](https://folio-org.atlassian.net/browse/FOLIO-4126)

The Workflows in this repository named `maven*.yml` are for building Maven-based back-end modules.
Docker images are published to FOLIO Docker Hub.
ModuleDescriptors are published to the FOLIO Registry.

Refer to example build system and workflows at https://github.com/folio-org/mod-settings

## Usage

Create a `.github/workflows` directory in the root of the module repository, and add a file named `maven.yml` with the following content.

> [!IMPORTANT]
> In-development, not ready for use. See above.

```yaml
name: Maven central workflow

on:
  push:
  pull_request:
  workflow_dispatch:

jobs:
  maven:
    uses: folio-org/.github/.github/workflows/maven.yml@FOLIO-4126-maven-workflows-1
    # Only handle push events from the main branch or tags, to decrease PR noise
    if: github.ref_name == github.event.repository.default_branch || github.event_name != 'push' || github.ref_type == 'tag'
    secrets: inherit
```

## Configuration

If there is a need to over-ride defaults, then add configuration variables to the single "with:" section of the module maven.yml Workflow.

Add the section at the end of the Workflow immediately after the "secrets" item.
For example:

```yaml
    # ...
    secrets: inherit
    with:
      java-version: '17'
      # Add configuration variables here if needed.
```

### Configuration: java-version

Optional. Default = '21'

```yaml
    with:
      java-version: '17'
```

### Configuration: publish-module-descriptor

Some Maven-based projects do not have a ModuleDescriptor.

Optional. Default = true

```yaml
    with:
      publish-module-descriptor: false
```

### Configuration: allow-snapshots-release

Normally a release must not use dependencies that are "snapshot" versions.

On rare occasions this might be needed.

Optional. Default = false

```yaml
    with:
      allow-snapshots-release: true
```

### Configuration: do-sonar-scan

Sonar can be disabled if that is needed, for example when a new project is not yet ready to commence the scans.

Optional. Default = true

```yaml
    with:
      do-sonar-scan: false
```

### Configuration: do-docker

Some Maven-based projects do not utilise Docker. If so then provide this variable as "false".

If this variable is "false", then also no ModuleDescriptor will be published.

> [!NOTE]
> See [Limitations - Only top-level Dockerfile](#only-top-level-dockerfile) at this stage.

Optional. Default = true

```yaml
    with:
      do-docker: false
```

### Configuration: docker-health-command

If this variable is provided, then the Docker Health Check will be run prior to the final building of the image.
If it fails, then no Docker image is built, and a ModuleDescriptor will not be published.

Optional. Default = None

```yaml
    with:
      docker-health-command: 'wget --no-verbose --tries=1 --spider http://localhost:8081/admin/health || exit 1'
```

### Configuration: docker-label-documentation

If not provided then the "org.opencontainers.image.documentation" label of the Docker image will be empty.

Optional. Default = None

```yaml
    with:
      docker-label-documentation: 'https://.../documentation.md'
```

## Limitations

### Only top-level Dockerfile

At this stage only a top-level Dockerfile is utilised. So these Workflows are not yet ready for projects that have lower-level Dockerfile.

## Docker image metadata

The docker image will have various labels automatically applied.

Note: If the "org.opencontainers.image.description" label of the generated image is empty, then that is because the module's GitHub repository is missing the "About" description in the top-right corner of its GitHub front page.
See advice at [Create a new FOLIO module and do initial setup](https://dev.folio.org/guidelines/create-new-repo/),
and bear in mind that Docker Hub imposes a [content length limit](https://github.com/peter-evans/dockerhub-description#content-limits) of 100 bytes for that short-description, so it will be truncated at that.

See also the  [Configuration: docker-label-documentation](#configuration-docker-label-documentation) variable.
