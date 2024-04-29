# Centralized UI Workflows

- [Methodology](#methodology)
- [Versioning](#versioning)
- [Using in your repository](#using-in-your-repository)
  - [Yarn v4](#yarn-v4)
    - [Windows shenanigans](#windows-shenanigans)
  - [Yarn v1 (deprecated)](#yarn-v1)
- [Configuration](#configuration)
  - [Node/Yarn configuration](#nodeyarn-configuration)
  - [Linting](#linting)
  - [Unit tests](#unit-tests)
    - [Jest](#jest)
    - [Bigtest](#bigtest)
  - [Sonarcloud](#sonarcloud)
  - [Translations](#translations)
  - [Module descriptor generation](#module-descriptor-generation)
  - [Module publication](#module-publication)
- [Secrets](#secrets)

This centralized workflow performs CI/CD tasks in FOLIO front-end repositories, including linting, test running, SonarCloud analysis, and deployment.

## Methodology

By separating each step into its own job, we can run them in parallel, which should speed up the overall process. This also allows us to easily add new steps in the future, and to run only the steps that are necessary for a given change.

Here are all of the individual jobs and how they interact:

![UML Diagram](./ui-diagram.png)

Solid arrows indicate jobs that must succeed for the next job to run. Dashed arrows indicate jobs that are optional (for example, we want Sonar to analyze even if tests fail).

## Versioning

This project uses semver, similar to how other FOLIO modules currently operate. Use the major version only, so new minor/patch releases are automatically picked up. Use `v1` for yarn v1; use `v2` for yarn v4.

## Using in your repository

To use the centralized workflow, create a `.github/workflows` directory in the root of your repository. If it exists already, replace the `build-npm.yml` and `build-npm-release.yml` with a single file, e.g. `ui.yml`, with the following content:

### yarn v4

```yaml
name: Centralized workflow
on:
  - push
  - pull_request
  - workflow_dispatch

jobs:
  ui:
    # Use the shared workflow from https://github.com/folio-org/.github
    uses: folio-org/.github/.github/workflows/ui.yml@v2
    # Only handle push events from the main branch, to decrease noise
    if: github.ref_name == github.event.repository.default_branch || github.event_name != 'push'
    secrets: inherit
```

If you are migrating from yarn v1, follow [yarn's v4 migration guide](https://yarnpkg.com/migration/guide), steps which are automated and customized for our build infrastructure in [this small bash script](https://gist.github.com/zburke/eb68b91506145185360b14aa6dcf9922). Mac/Linux users should be able to run it as is; Windows users will need to run it like `bash yarn-v4.sh`. To perform the minimal steps manually:
```sh
corepack enable
corepack prepare yarn@4.1.1
yarn set version berry
yarn config set npmScopes.folio.npmRegistryServer https://repository.folio.org/repository/npm-folioci/
yarn config set nodeLinker node-modules
git rm .npmrc
yarn add -D jest@^29 jest-environment-jsdom@^29
git add package.json .yarnrc.yml yarn.lock .yarn/releases
```
This will download v4 binaries and make them available but will leave 1.x as your default for projects that haven't specifically chosen to upgrade to v4.

#### Windows shenanigans

Windows users, note [this `corepack` bug](https://github.com/nodejs/corepack/issues/334) and [manual workaround](https://github.com/nodejs/corepack/issues/334#issuecomment-1906658423): the `yarn set version berry` step will fail with `Internal Error: ENOENT: no such file or directory, stat 'C:\Users\{username}\AppData\Local\node\corepack\yarn\{yarn version}'`. Corepack on Windows is fussy but the following steps seem to allow v1 and v4 to peacefully coexist.

* In an admin console powershell, run `corepack enable` then `corepack disable`. This will create `C:\Users\{username}\AppData\Local\node\corepack`.
* In Windows Explorer go to `C:\Users\{username}\AppData\Local\node\corepack`. There will be one or more directories named `corepack-xxxx-xxxxxxxxxx`. Open one and copy the two files (`yarn.js`, `.corepack`).
* In Windows Explorer go to `C:\Users\{username}\AppData\Local\node\corepack\yarn`. Create a new directory corresponding to your `yarn` version, e.g. `4.1.1` and paste the two files.
* In an admin console powershell, run `corepack enable`
* In a non-admin console powershell, in your project directory, run `yarn set verion berry`
* To confirm this was successful, run `yarn -v`; you should see something like `4.1.1`.
* To switch to v1 in another project, run `yarn set version 1.22`. Contrary to the documentation, `yarn set version latest` does not work.

### yarn v1 (deprecated)
The `.github/workflows/ui.yml` file is identical to that specified above except that it references `v1` of this repository:
```yaml
uses: folio-org/.github/.github/workflows/ui.yml@v1
```

## Configuration

There are many configuration variables, to allow customization of the workflow to your repository. To use these, add inputs under `with:` in the workflow, for example:

```yaml
# ...
if: github.ref_name == github.event.repository.default_branch || github.event_name != 'push'
secrets: inherit
with:
  jest-test-command: yarn test --ci --color --coverage
```

> [!CAUTION]
> In general, **it is preferred that you alter your repository to not need to customize the workflow**, particularly for file paths for coverage/etc. This will make it easier to update the workflow in the future, and will help to standardize the CI/CD process across the FOLIO front-end repositories.

### Node/Yarn configuration

| Input                | Type   | Default                                                                                                                 | Description                                    |
| -------------------- | ------ | ----------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------- |
| `node-version`       | string | `18.x`                                                                                                                  | The Node version to install                    |
| `folio-npm-registry` | string | `https://repository.folio.org/repository/npm-folioci/` or `https://repository.folio.org/repository/npm-folio/` for tags | The repository to pull `@folio/` packages from |

### Linting

| Input               | Type    | Default | Description                                                  |
| ------------------- | ------- | ------- | ------------------------------------------------------------ |
| `allow-lint-errors` | boolean | false   | If set to `true`, PRs will not be blocked by linting errors. |

### Unit tests

Jest, Bigtest, or both can be run as part of the workflow. The default is to run only Jest; you can change this via `jest-enabled` and `bigtest-enabled`. If you need both, they should be runnable as separate commands (set via `jest-test-command` and `bigtest-test-command`).

#### Jest

| Input                      | Type    | Default                    | Description                                                                                                     |
| -------------------------- | ------- | -------------------------- | --------------------------------------------------------------------------------------------------------------- |
| `jest-enabled`             | boolean | `true`                     | Whether to run Jest tests                                                                                       |
| `jest-test-command`        | string  | `yarn test`                | The command to use to run Jest tests                                                                            |
| `jest-junit-output-dir`    | string  | `artifacts/jest-junit`     | Directory where Jest reports are stored, without a trailing slash                                               |
| `jest-coverage-report-dir` | string  | `artifacts/coverage-jest/` | Directory in which Jest coverage reports are stored in, with trailing slash; LCOV data should be in "lcov.info" |

#### Bigtest

| Input                         | Type    | Default               | Description                                                                                                     |
| ----------------------------- | ------- | --------------------- | --------------------------------------------------------------------------------------------------------------- |
| `bigtest-enabled`             | boolean | `false`               | Whether to run Bigtest tests                                                                                    |
| `bigtest-test-command`        | string  | `yarn test`           | The command to use to run Bigtest tests                                                                         |
| `bigtest-junit-output-dir`    | string  | `artifacts/runTest`   | Directory where Bigtest reports are stored, without a trailing slash                                            |
| `bigtest-coverage-report-dir` | string  | `artifacts/coverage/` | Directory in which Jest coverage reports are stored in, with trailing slash; LCOV data should be in "lcov.info" |

### Sonarcloud

| Input              | Type   | Default   | Description                                                                                                                                                                                                                                 |
| ------------------ | ------ | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `sonar-sources`    | string | `./src`   | A comma-separated list of directories containing code to analyze (no wildcards or whitespace); see [Sonar's documentation](https://docs.sonarsource.com/sonarcloud/advanced-setup/analysis-parameters/#analysis-scope) for more information |
| `sonar-exclusions` | string | See below | A comma-separated list of wildcards to exclude from analysis. You can use whitespace as needed for formatting.                                                                                                                              |

<details>
<summary>Default value for `sonar-exclusions`</summary>

The comments (lines starting with `#`) are not part of the value, but are included here for clarity.

```
 # documentation
docs/**,
examples/**,
**/*.md,
**/stories/*,
**/.stories.*,
LICENSE,

 # build/CI artifacts
artifacts/**,
ci/**,
node_modules/**,

 # tests
**/tests/**,
**/test/**,
resources/bigtest/interactors/**,
resources/bigtest/network/**,
**/*-test.*,
**/*.test.*,
karma.conf.*,
jest.config.*,

 # misc
*.css,
*.json,
```

</details>

### Translations

| Input                  | Type    | Default | Description                                                                                              |
| ---------------------- | ------- | ------- | -------------------------------------------------------------------------------------------------------- |
| `compile-translations` | boolean | `true`  | Whether to compile translation files; if disabled, none will be included in the published module, either |

### Module descriptor generation

| Input                        | Type    | Default                                | Description                                                               |
| ---------------------------- | ------- | -------------------------------------- | ------------------------------------------------------------------------- |
| `generate-module-descriptor` | boolean | `true`                                 | Whether to generate a module descriptor (will be included in NPM package) |
| `publish-module-descriptor`  | boolean | `true`                                 | Whether to publish the module descriptor to `module-descriptor-registry`  |
| `module-descriptor-registry` | string  | `https://folio-registry.dev.folio.org` | The Okapi instance to publish module descriptors to                       |

### Module publication

| Input                     | Type   | Default                                                                                            | Description                                                                                                                            |
| ------------------------- | ------ | -------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `folio-npm-registry-auth` | string | `//repository.folio.org/repository/npm-folioci/` or `//repository.folio.org/repository/npm-folio/` | The key to store the authentication under for the FOLIO NPM registry                                                                   |
| `publish-exclusions`      | string | `artifacts`, `.github`, and `.scannerwork`                                                         | A newline-separated list of files and directories to leave out of the published package, in addition to your repository's `.npmignore` |

## Secrets

These are automatically passed from your repository by adding `with: secrets` to the workflow; they are here solely for documentation.

| Secret                    | Description                               |
| ------------------------- | ----------------------------------------- |
| `SONAR_TOKEN`             | Token for performing SonarCloud analysis  |
| `DOCKER_USER`             | Username for pulling Okapi docker image   |
| `DOCKER_PASSWORD`         | Password for pulling Okapi docker image   |
| `NPM_TOKEN`               | Token for publishing to NPM               |
| `FOLIO_REGISTRY_USERNAME` | Username for publishing module descriptor |
| `FOLIO_REGISTRY_PASSWORD` | Password for publishing module descriptor |
