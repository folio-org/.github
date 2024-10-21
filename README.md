# Shared GitHub Workflows for FOLIO

## Introduction

The directory [`workflow-templates`](workflow-templates) contains templates for the general CI/CD workflows for the project.

The directory [`.github/workflows`](.github/workflows) contains centralized workflows which are currently being implemented.

## Status

The front-end `ui-` and `stripes-` repositories are currently being migrated to the centralized workflow in this repository.

Workflows for back-end repositories are not yet in progress (they continue to use Jenkins).

Workflows for Go-based back-end repositories are currently being implemented at mod-reporting.

## Documentation

Refer to the various types of centralized workflows, including their setup and configuration:

* [README-UI.md](README-UI.md) -- for the front-end repositories.
* [README-docker.md](README-docker.md) -- for repositories that only have a Dockerfile.
* [README-go.md](README-go.md) and [README-go-lint.md](README-go-lint.md) -- for Go-based back-end repositories.
