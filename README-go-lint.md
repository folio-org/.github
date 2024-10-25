# Centralised GitHub Workflows for Go linting

<!-- ../okapi/doc/md2toc -l 2 -h 3 README-go-lint.md -->
* [Introduction](#introduction)
* [Usage](#usage)
* [Configuration](#configuration)
* [Linting and code analysis jobs](#linting-and-code-analysis-jobs)
    * [errcheck](#errcheck)
    * [deadcode](#deadcode)
    * [govulncheck](#govulncheck)
    * [staticcheck](#staticcheck)
    * [golangci-lint](#golangci-lint)
* [Additional notes](#additional-notes)
    * [Version of Go](#version-of-go)

## Introduction

The Workflows in this repository named `go-lint*.yml` are for linting and code analysis for Go-based back-end projects.

A set of important linting facilities are configured by default.
The project can also configure additional relevant facilities as explained in the following [Configuration](#configuration) sections.

Refer to example workflows at https://github.com/folio-org/mod-reporting

(Refer to the main [README-go](README-go.md) for the build and deployment facilities.)

## Usage

Create a `.github/workflows` directory in the root of the module repository, and add a file named `go-lint.yml` with the following content.
The filename is not important -- it can be anything.

```yaml
name: Go lint central workflow

on:
  push:
    branches: ['*']
    paths:
      - '**.go'
      - 'go.*'
      - 'src/.errcheck-exclude'
      - '**/.golangci.yml'
      - '**/staticcheck.conf'
      - '.github/workflows/go-lint.yml'
  workflow_dispatch:

jobs:
  go-lint:
    uses: folio-org/.github/.github/workflows/go-lint.yml@master
    with:
      errcheck-excludes-file: 'src/.errcheck-exclude'
      golangci-config-file: 'src/.golangci.yml'
```

The Workflow will operate on any branch. It is only triggered on changes to one of the listed "paths".

## Configuration

There are two configuration variables, both optional.

`errcheck-excludes-file` -- The path to a file containing a list of functions to be excluded with the "errcheck" facility. See notes in the following [errcheck](#errcheck) section.

`golangci-config-file` -- The path to a configuration file for the "golangci-lint" facility. The default path is a top-level `.golangci.yml` file (which is automatically discovered). This variable enables specification of an alternative path. See notes in the following [golangci-lint](#golangci-lint) section.

## Linting and code analysis jobs

The centralised [go-lint.yml](.github/workflows/go-lint.yml) Workflow has separate jobs, each described in the following sections.

Each job will report a summary of detected issues to the "Annotations" section of the Workflow run Summary output, and more information in the output of its respective "Run" step.

### errcheck

Reports unchecked errors.

The Workflow [go-lint-errcheck.yml](.github/workflows/go-lint-errcheck.yml)

Documentation:

* https://github.com/kisielk/errcheck

As noted in the previous [Configuration](#configuration) section, an optional "excludes" file can be provided.

### deadcode

Reports unreachable functions.

The Workflow [go-lint-deadcode.yml](.github/workflows/go-lint-deadcode.yml)

Documentation:

* https://pkg.go.dev/golang.org/x/tools/cmd/deadcode

### govulncheck

Vulnerability management.

The Workflow [go-lint-govulncheck.yml](.github/workflows/go-lint-govulncheck.yml)

Documentation:

* https://github.com/golang/vuln

This tool utilises the Go language version specified in the project's `./go.mod` file. If the project uses an older version of Go then this tool will be reporting vulnerabilities in the standard libraries, which have probably been fixed in the current Go language version.

It also reports vulnerabilities in functions of dependencies that are utilised by the project code.

### staticcheck

The Workflow [go-lint-staticcheck.yml](.github/workflows/go-lint-staticcheck.yml)

Documentation:

* https://github.com/dominikh/go-tools
* https://staticcheck.dev/docs/

The default `staticcheck.conf` configuration files can be utilised to specify various [checks](https://staticcheck.dev/docs/checks/) to be excluded or non-default ones to also be included, etc.
Refer to the staticcheck documentation for [Configuration files](https://staticcheck.dev/docs/configuration/#configuration-files).

### golangci-lint

Runs various linters in parallel.

The Workflow [go-lint-golangci.yml](.github/workflows/go-lint-golangci.yml)

Documentation:

* https://github.com/golangci/golangci-lint
* https://golangci-lint.run/

The linters [Enabled by default](https://golangci-lint.run/usage/linters/#enabled-by-default) are `gosimple` and `govet` and `ineffassign` and `unused`.
(The other normal defaults `errcheck` and `staticcheck` are disabled here because our 'go-lint' runs them as separate jobs.)

As noted in the previous [Configuration](#configuration) section, a default optional configuration file is automatically discovered at the top-level path to `.golangci.yml` file. An optional variable can be used to specify an alternative path to a "configuration" file.

If needed then the configuration file can be used to provide various configuration parameters for the enabled linters.
Refer to the golangci-lint [Configuration](https://golangci-lint.run/usage/configuration/) documentation.

Refer to the list of all available [linters](https://golangci-lint.run/usage/linters/) and their configuration options.

As a basic example, the additional linter "whitespace" can be enabled:

```
linters:
  enable:
    - whitespace
```

## Additional notes

### Version of Go

The top-level `go.mod` file is consulted for the Go version to be used.

