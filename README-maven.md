# Centralised GitHub Workflows for Maven

<!-- ../okapi/doc/md2toc -l 2 -h 3 README-maven.md -->
* [Introduction](#introduction)
* [Usage](#usage)

## Introduction

NOTE: In-development

The Workflows in this repository named `maven*.yml` are for building Maven-based back-end modules.
Docker images are published to FOLIO Docker Hub.
ModuleDescriptors are published to the FOLIO Registry.

Refer to example build system and workflows at https://github.com/folio-org/mod-settings

## Usage

Create a `.github/workflows` directory in the root of the module repository, and add a file named `maven.yml` with the following content.

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


