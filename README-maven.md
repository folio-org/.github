# Centralised GitHub Workflows for Maven

<!-- ../okapi/doc/md2toc -l 2 -h 3 README-maven.md -->
* [Introduction](#introduction)
* [Usage](#usage)

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
    branches: ['*']
    tags:
      - '[vV][0-9]+.[0-9]+.[0-9]+*'
  workflow_dispatch:

jobs:
  maven:
    uses: folio-org/.github/.github/workflows/maven.yml@FOLIO-4126-maven-workflows-1
    secrets: inherit
```

## Configuration

If there is a need to over-ride defaults, then add configuration variables to the "with:" section of the module maven.yml Workflow.

Add the section at the end of the Workflow immediately after the "secrets" item.
For example:

```yaml
    # ...
    secrets: inherit
    with:
      docker-label-documentation: 'https://.../documentation.md'
      # Add configuration variables here if needed.
```

### Configuration: allow-snapshots-release

Normally a release must not use dependencies that are "snapshot" versions.

On rare occasions this might be needed.

Default = false

```yaml
    with:
      allow-snapshots-release: true
```
