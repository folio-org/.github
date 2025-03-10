# Centralised GitHub Workflows for Go

<!-- ../okapi/doc/md2toc -l 2 -h 3 README-go.md -->
* [Introduction](#introduction)
* [Usage](#usage)
* [Image metadata](#image-metadata)
* [Configuration](#configuration)
* [Additional notes](#additional-notes)
    * [Version of Go](#version-of-go)
    * [SonarCloud code analysis](#sonarcloud-code-analysis)
    * [Repository branch protection](#repository-branch-protection)

## Introduction

The Workflows in this repository named `go*.yml` are for building Go-based back-end modules.
Docker images are published to FOLIO Docker Hub.
ModuleDescriptors are published to the FOLIO Registry.

It is expected that Go-based modules use the [Make](https://en.wikipedia.org/wiki/Make_(software)) utility for build operations.

Refer to example build system and workflows at https://github.com/folio-org/mod-reporting

Refer to [README-go-lint](README-go-lint.md) for using linting and code analysis facilities.

## Usage

Create a `.github/workflows` directory in the root of the module repository, and add a file named `go.yml` with the following content.
The filename is not important -- it can be anything.

```yaml
name: Go central workflow

on:
  push:
    branches: ['*']
    paths:
      - '**.go'
      - 'go.*'
      - 'etc/config.json'
      - '**/Makefile'
      - 'Dockerfile'
      - 'descriptors/ModuleDescriptor-template.json'
      - 'sonar-project.properties'
      - '.github/workflows/go.yml'
    tags:
      - '[vV][0-9]+.[0-9]+.[0-9]+*'
  workflow_dispatch:

jobs:
  go:
    uses: folio-org/.github/.github/workflows/go.yml@v1
    secrets: inherit
```

The Workflow will operate on any branch. It is only triggered on changes to one of the listed "paths" or when release git tags are pushed.

On a non-mainline branch it will build the project, build the docker image but not push it to Docker Hub, generate the ModuleDescriptor, conduct the Okapi dependency check.

On the mainline branch, the docker image will be pushed to Docker Hub, and the ModuleDescriptor will be posted to the FOLIO MD Registry.

When git tags are pushed for a release, then the docker image is published to the "folioorg" repository of FOLIO Docker Hub.

Otherwise the snapshot docker image is published to the "folioci" repository of FOLIO Docker Hub.\
For example: https://hub.docker.com/r/folioci/mod-reporting

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

## Additional notes

### Version of Go

The top-level `go.mod` file is consulted for the Go version to be used.

### SonarCloud code analysis

For a new project repository, if not yet ready for code analysis then add the following configuration variable to a "with:" section at the end of the Workflow after the "secrets" item:

```yaml
...
    with:
      ...
      sonar-enabled: false
```

When ready for code analysis, then follow these steps:
1. [Request](https://dev.folio.org/faqs/how-to-raise-devops-ticket/) FOLIO DevOps to create a new project at SonarCloud.
2. Add the sonar-project.properties file at the top-level of the source-code repository. Follow the example at [mod-reporting](https://github.com/folio-org/mod-reporting/blob/main/sonar-project.properties)

Go-based projects use the [FOLIO Go way](https://sonarcloud.io/organizations/folio-org/quality_profiles/show?name=FOLIO+Go+way&language=go) Sonar Quality Profile.

Note that Sonar Go code analysis is not yet mature.
(Refer to [README-go-lint](README-go-lint.md) for using other linting and code analysis facilities.)

Refer to some basic notes about [Sonar rule customization](https://dev.folio.org/guides/code-analysis/#rule-customization). Take care if using the crude `// NOSONAR` exclusion technique.

### Repository branch protection

If the module's Workflow utilises "on:push:paths:" to trigger the Workflow run, then branch protection "required checks" for pull-requests are ineffective.
Perhaps will be able to utilise
"[Repository rules](https://github.blog/changelog/2023-08-02-github-actions-required-workflows-will-move-to-repository-rules/)"
or "[policy-bot](https://github.com/palantir/policy-bot)"
or "[paths-filter](https://github.com/dorny/paths-filter)".

Use common-sense approach to not merge if there is a failed branch Workflow run.

