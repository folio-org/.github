
# Guidelines for security

> [!IMPORTANT]
> This document is still in development.

These are some guidelines regarding the security aspects of the Workflows.

<!-- $GH_FOLIO/okapi/doc/md2toc -l 2 -h 3 guidelines-security.md -->
* [Intermediate environment variables](#intermediate-environment-variables)
* [yarn install](#yarn-install)
* [Specific SHA for folio-tools](#specific-sha-for-folio-tools)
* [Prefer locally-stored scripts](#prefer-locally-stored-scripts)

## Intermediate environment variables

Use intermediate environment variables rather that variable interpolation of input variables in the `run:`` steps.

See ticket [FOLIO-4489](https://folio-org.atlassian.net/browse/FOLIO-4489).

Refer to an example at "docker-push":
[workflows/docker-publish.yml#L18](https://github.com/folio-org/.github/blob/a3d7eeb768269e0d919a90530834572e0043fda0/.github/workflows/docker-publish.yml#L18) \
[workflows/docker-publish.yml#L38](https://github.com/folio-org/.github/blob/a3d7eeb768269e0d919a90530834572e0043fda0/.github/workflows/docker-publish.yml#L38)


## yarn install

Use `yarn install --frozen-lockfile --ignore-scripts --non-interactive`

## Specific SHA for folio-tools

For any supporting scripts from the folio-tools repository, use a specific git SHA for the checkout.

## Prefer locally-stored scripts

If a script cannot be handled inline, then use a script stored in the local [scripts](scripts) directory.

For more complex scripts, store them in the folio-tools repository.
