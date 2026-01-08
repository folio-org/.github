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

## Development

### Use actionlint prior to commit

Install [actionlint](https://github.com/rhysd/actionlint#quick-start).

While developing Workflows run actionlint prior to each commit. It is very helpful for identifying syntax and mis-configuration problems, which are otherwise difficult to diagnose.

It includes "shellcheck". The following typical invocation skips some well-known shellcheck basic issues:

```
SHELLCHECK_OPTS='--exclude=SC2086,SC2046' actionlint *.yml
```

## Further information

* [Release procedure](docs/release-procedure.md)

