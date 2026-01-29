# Release procedure

Assume that the current release is "v1.6.0" and that the intent is to make the next minor release, i.e. "v1.7.0".

Accumulate entries in [CHANGELOG](../CHANGELOG.md).

## Overview

The release is tagged three times:

* Create the actual release with an annotated tag with the three-part "v1.7.0"
* Create a new lightweight tag "v1.7" which corresponds with the release.
* Move the existing lightweight tag "v1" to correspond with the release.

See similar steps for [patch release](#similarly-for-a-patch-release).

## Create new annotated tag for release via GitHub

Assemble the release notes as a snippet of Markdown format with a similar structure to the previous version. Note the pull-requests that added the main changes.

Perform the actual release via the GitHub UI at https://github.com/folio-org/.github/releases i.e. select "Draft a new release".

If the "Target" is not the current head of mainline branch, the find the SHA of the relevant commit.

```
Tag: v1.7.0
Target: master
Title: v1.7.0
Set as latest release: Yes
Description: ...
```

Do "Publish release". This will tag the mainline as "v1.7.0".

## Create new lightweight tag for minor version

```
git pull
git tag v1.7
git push origin v1.7
```

## Move existing lightweight tag for major version

```
git tag -d v1          # delete the old local tag
git push origin :v1    # delete the old remote tag
git tag v1             # create a new local tag at HEAD
git push origin v1     # push new tag to remote
```

## Ensure that tags are appropriate

Verify that the three tags are the same SHA at https://github.com/folio-org/.github/tags

## Similarly for a patch release

When a "patch" release is needed (e.g. for "v1.6.1") then follow a similar procedure:

* Create the actual release with an annotated tag with the three-part "v1.6.1"
* Move the existing lightweight tag "v1.6" to correspond with the release.
  * As for [Move existing lightweight tag for major version](#move-existing-lightweight-tag-for-major-version) but using "v1.6" rather than "v1".
* Move the existing lightweight tag "v1" to correspond with the release.
  * i.e. As at [Move existing lightweight tag for major version](#move-existing-lightweight-tag-for-major-version)

