
# Guidelines for security

> [!IMPORTANT]
> This document is still in development.

These are some guidelines regarding the security aspects of the Workflows.

<!-- $GH_FOLIO/okapi/doc/md2toc -l 2 -h 3 guidelines-security.md -->
* [Intermediate environment variables](#intermediate-environment-variables)
* [yarn install](#yarn-install)
* [Specific version for Okapi](#specific-version-for-okapi)
* [Use secret for module-descriptor-registry](#use-secret-for-module-descriptor-registry)
* [Specific SHA for folio-tools](#specific-sha-for-folio-tools)
* [Prefer locally-stored scripts](#prefer-locally-stored-scripts)
* [Double-quote all variables in run steps](#double-quote-all-variables-in-run-steps)

## Intermediate environment variables

Use intermediate environment variables rather than variable interpolation of input variables in the `run:` steps.

See ticket [FOLIO-4489](https://folio-org.atlassian.net/browse/FOLIO-4489).

Refer to an example at "docker-push": \
[workflows/docker-publish.yml#L18](https://github.com/folio-org/.github/blob/a3d7eeb768269e0d919a90530834572e0043fda0/.github/workflows/docker-publish.yml#L18) \
[workflows/docker-publish.yml#L38](https://github.com/folio-org/.github/blob/a3d7eeb768269e0d919a90530834572e0043fda0/.github/workflows/docker-publish.yml#L38)

## yarn install

Use `yarn install --frozen-lockfile --ignore-scripts --non-interactive`

## Specific version for Okapi

Use a specific version for Okapi, rather than `okapi:latest`.
At the various `*-descriptor-verify.yml` Workflows.

## Use secret for module-descriptor-registry

Use a secret rather than hardcoded URL.
At the various `*-module-descriptor-publish.yml` Workflows.

## Specific SHA for folio-tools

For any supporting scripts from the folio-tools repository, use a specific git SHA for the checkout.

## Prefer locally-stored scripts

If a script cannot be handled inline, then use a script stored in the local [scripts](scripts) directory.

For more complex scripts, store them in the folio-tools repository.

## Double-quote all variables in run steps

There are many cases where 'actionlint' complains, e.g.

```
docker-publish.yml:39:9: shellcheck reported issue in this script:
SC2086:info:4:79: Double quote to prevent globbing and word splitting
```

This arises due to not double-quoting the `$GITHUB_STEP_SUMMARY` and similar variables when doing echo to them during a run step.

Fixing this across the board will enable to stop avoiding `SC2086` in the local actionlint [invocation](https://github.com/folio-org/.github/tree/master#use-actionlint-prior-to-commit) and the [actionlint.yml](https://github.com/folio-org/.github/blob/7505c4cc4dad8e6013c76ca5fa9d877caeee2436/.github/workflows/_actionlint.yml#L27) Workflow.
That will allow actionlint to report other situations which might be important.



