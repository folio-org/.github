# Centralised GitHub Workflows for Gradle

<!-- ../okapi/doc/md2toc -l 2 -h 3 README-gradle.md -->
* [Introduction](#introduction)
* [Usage](#usage)
* [Configuration](#configuration)
    * [Configuration: artifact-id](#configuration-artifact-id)
    * [Configuration: publish-module-descriptor](#configuration-gradle-directory)
    * [Configuration: allow-snapshots-release](#configuration-module-descriptor-registry)
    * [Configuration: apt-packages](#configuration-java-version)
    * [Configuration: apt-packages](#configuration-publish-module-descriptor)
    * [Configuration: docker-health-command](#configuration-docker-health-command)
    * [Configuration: docker-label-documentation](#configuration-docker-label-documentation)
* [Docker image metadata](#docker-image-metadata)
* [Install the caller Workflow](#install-the-caller-workflow)
* [Release procedures](#release-procedures)
  * [Release procedures FAQ](#release-procedures-faq)
* [Limitations](#limitations)
    * [Only top-level Dockerfile](#only-top-level-dockerfile)
* [Oddities](#oddities)
    * [Timeout at ModuleDescriptor registry](#timeout-at-moduledescriptor-registry)

## Introduction

The Workflows in this repository named `gradle*.yml` are for building Maven-based back-end modules.
Docker images are published to FOLIO Docker Hub.
ModuleDescriptors are published to the FOLIO Registry.

Refer to example build system and workflows at https://github.com/folio-org/mod-agreements

## Usage

Create a `.github/workflows` directory in the root of the module repository, and add a file named `gradle.yml` with the following content.

If there is already a workflow named gradle.yml for verifying basic Maven builds, then rename that file.
It will ease management to have the same filename at every repository.

Follow [Install the caller Workflow](#install-the-caller-workflow) section below to install the initial workflow.

After the first Actions run, do not rename the filename of this caller workflow, as that will reset the GitHub run number and so wreck the sequential order of the ModuleDescriptor identifiers.


```yaml
# https://github.com/folio-org/.github/blob/master/README-gradle.md

name: Gradle Central Workflow

on:
  push:
    branches: ['FOLIO-4554-gradle-workflows']
  pull_request:
  workflow_dispatch:

jobs:
  gradle:
    uses: folio-org/.github/.github/workflows/gradle.yml@FOLIO-4554-gradle-workflows
    # Only handle push events from the main branch or tags, to decrease PR noise
    if: github.ref_name == github.event.repository.default_branch || github.event_name != 'push' || github.ref_type == 'tag' || github.ref_name == 'FOLIO-4554-gradle-workflows'
    secrets: inherit
    with:
      artifact-id: mod-agreements
      docker-label-documentation: https://github.com/folio-org/mod-agreements/tree/master/docs
```

## Configuration

If there is a need to over-ride defaults, then add configuration variables to the single "with:" section of the module gradle.yml Workflow.

Add the section at the end of the Workflow immediately after the "secrets" item.
For example:

```yaml
    # ...
    secrets: inherit
    with:
      java-version: '17'
      # Add configuration variables here if needed.
```
### Configuration: artifact-id

This is the name of the module being built e.g mod-agreements. It's required as it's the name of the build artifact and docker image that would be built and pushed.

```yaml
    with:
      artifact-id: mod-agreements
```

### Configuration: gradle-directory

The directory containing the Gradle project to be built.

Optional. Default = 'service'

```yaml
    with:
      gradle-directory: service
```

### Configuration: module-descriptor-registry

Okapi Module descriptor registry URL

Optional. Default = 'https://folio-registry.dev.folio.org'

```yaml
    with:
      module-descriptor-registry: 'https://folio-registry.dev.folio.org'
```

### Configuration: java-version

Allowed values: 17 or 21 or 25

Optional. Default = '21'

```yaml
    with:
      java-version: '17'
```

### Configuration: publish-module-descriptor

Some Gradle-based projects do not have a ModuleDescriptor.

Optional. Default = true

```yaml
    with:
      publish-module-descriptor: false
```

### Configuration: docker-health-command

If this variable is provided, then the Docker Health Check will be run prior to the final building of the image. If it fails, then no Docker image is built, and a ModuleDescriptor will not be published.


Note that the workflow will utilise this variable if provided, but does not enforce it.
The status will be reported to the workflow "Summary".

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

## Docker image metadata

The docker image will have various labels automatically applied.

Note: If the "org.opencontainers.image.description" label of the generated image is empty, then that is because the module's GitHub repository is missing the "About" description in the top-right corner of its GitHub front page.
See advice at [Create a new FOLIO module and do initial setup](https://dev.folio.org/guidelines/create-new-repo/),
and bear in mind that Docker Hub imposes a [content length limit](https://github.com/peter-evans/dockerhub-description#content-limits) of 100 bytes for that short-description, so it will be truncated at that.

See also the  [Configuration: docker-label-documentation](#configuration-docker-label-documentation) variable.

## Install the caller Workflow

> [!NOTE]
> If there is not yet a JIRA ticket at the co-ordination Epic [FOLIO-4554](https://folio-org.atlassian.net/browse/FOLIO-4554) then please raise one in a similar manner to the others, and add that as the Parent.

Create a new branch at the module repository.

Create a file at `.github/workflows/gradle.yml` as explained at the [Usage](#usage) section.

Add other [Configuration](#configuration) variables to suit the needs of the module, e.g. `docker-health-command` variable.
Align properties with the old Jenkinsfile (noting the defaults shown in the [Configuration](#configuration) section).

Do `git mv Jenkinsfile Jenkinsfile-disabled` (so that it can be restored quickly if needed, and still be able to review its properties).

Commit and push.

(If it is desired to do a branch run prior to raising the pull-request, then "dispatch" the workflow on that branch.
However the line 13 "if:" will need to be temporarily commented-out for one run, because the workflow does not yet exist on mainline branch.)

Raise the pull-request, and review the run results.



If assistance is needed with "Branch protection" then [contact](https://dev.folio.org/faqs/how-to-raise-devops-ticket/#general-folio-devops) FOLIO DevOps and advise the checks that you need.

Wait until after the next "Platform build" to give some time if things go amiss.
https://dev.folio.org/guides/automation/#platform-hourly-build (finishes approx 53m past)
https://github.com/folio-org/platform-complete/commits/snapshot/

Merge and watch the mainline branch run.

Review the results for the Docker image and ModuleDescriptor. The identifier for all modules will use base number 2000 plus the sequential workflow run_number (e.g. 2002 for the second run).

Visit the following resources (adjusted for the relevant repository name):
* https://hub.docker.com/r/folioci/mod-agreements/tags
* https://hub.docker.com/r/folioci/mod-agreements (for new generated description)
* https://folio-registry.dev.folio.org/_/proxy/modules?filter=mod-agreements&latest=1
* https://folio-registry.dev.folio.org/_/proxy/modules?filter=mod-agreements&latest=1&full=true
* https://repository.folio.org/#browse/browse:maven-snapshots:org%2Ffolio%2Fmod-agreements
* https://sonarcloud.io/project/overview?id=org.folio:mod-agreements

Await success of the subsequent "Platform hourly build" and see snapshot branch updated.

If there is a need to quickly revert to Jenkins-based build, then [delete](https://github.com/folio-org/mod-settings/blob/master/.github/workflows/delete-test-md.yml) the published ModuleDescriptor (with great care), re-configure the branch protection checks, restore the Jenkinsfile.

## Release procedures



### Release procedures FAQ



## Limitations

### Only top-level Dockerfile

At this stage only a top-level Dockerfile is utilised. So these Workflows are not yet ready for projects that have lower-level Dockerfile.

## Oddities

### Timeout at ModuleDescriptor registry

Occasionally the job to "Publish ModuleDescriptor" gets a timeout at the registry.

In this case the Docker image would be published but not the associated ModuleDescriptor.

Either re-run that failed job, or "dispatch" the complete workflow again to publish a new Docker image and ModuleDescriptor.
