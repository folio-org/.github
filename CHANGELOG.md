# Change history for .github

## IN PROGRESS

* Add CHANGELOG, reference Being John Malkovich.

## [1.9.0](https://github.com/folio-org/.github/tree/v1.9.0) (2025-02-18)
[Full Changelog](https://github.com/folio-org/.github/compare/v1.8.0...v1.9.0)

* FOLIO-4190 - migrate to sonarcube-scan-action in #77

## [1.8.0](https://github.com/folio-org/.github/tree/v1.8.0) (2025-02-13)
[Full Changelog](https://github.com/folio-org/.github/compare/v1.7.0...v1.8.0)

* FOLIO-3994 go-lint: Upgrade golangci-lint to v1.63 - by @dcrossleyau in #69
* FOLIO-4126 FOLIO-4136 Commence top-level maven Workflow - by @dcrossleyau in #70
* FOLIO-4126 Emphasise maven Workflows are in-development - by @dcrossleyau in #71
* FOLIO-3994 Use sonarqube-scan-action - by @dcrossleyau in #72
* FOLIO-4039 Use consistent variable name publish-module-descriptor - by @dcrossleyau in #73
* FOLIO-3203 api-doc: trigger on ModuleDescriptor changes - by @dcrossleyau in #74
* FOLIO-3994 go-lint: Use specific patch version golangci-lint - by @dcrossleyau in #75
* FOLIO-3994 go-lint: Use staticcheck 2025.1 - by @dcrossleyau in #76

## [1.7.0](https://github.com/folio-org/.github/tree/v1.7.0) (2024-11-26)
[Full Changelog](https://github.com/folio-org/.github/compare/v1.6.0...v1.7.0)

* FOLIO-3994 FOLIO-4083 Improve READMEs Go - by @dcrossleyau in #59
* FOLIO-4109 Document the release procedure - by @dcrossleyau in #60
* FOLIO-4126 Remove partially implemented Maven workflow templates - by @dcrossleyau in #63
* STRIPES-951 Delete NPM workflow templates - by @julianladisch in #64
* Fix UI workflow documentation typo - by @ncovercash in #65
* FOLIO-3994 go-lint: Upgrade golangci-lint to v1.62 - by @dcrossleyau in #66
* FOLIO-4039 Force fetch git tags - by @dcrossleyau in #68

## [1.6.0](https://github.com/folio-org/.github/tree/v1.6.0) (2024-10-28)
[Full Changelog](https://github.com/folio-org/.github/compare/v1.5.0...v1.6.0)

* [FOLIO-4039] Update example at README-docker - by @dcrossleyau in #54
* [FOLIO-4086] Fix shared UI GitHub Actions workflow not running for tags - by @ncovercash in #55
* [FOLIO-3994 FOLIO-4083] Initial go-lint centralised Workflows - by @dcrossleyau in #56
* [FOLIO-3994 FOLIO-4083] Improve README-go-lint - by @dcrossleyau in #57
* [FOLIO-3994 FOLIO-4083] Improve README-go-lint - by @dcrossleyau in #58

## [1.5.0](https://github.com/folio-org/.github/tree/v1.5.0) (2024-09-25)
[Full Changelog](https://github.com/folio-org/.github/compare/v1.4.1...v1.5.0)

* [FOLIO-3994] Go Workflows explain SonarCloud configuration by @dcrossleyau in #51
* [FOLIO-3994] Document Sonar FOLIO Go way by @dcrossleyau in #52
* [FOLIO-4077] Freeze lockfile when running yarn install by @ncovercash in #53

## [1.4.1](https://github.com/folio-org/.github/tree/v1.4.1) (2024-09-20)
[Full Changelog](https://github.com/folio-org/.github/compare/v1.4.0...v1.4.1)

* [FOLIO-4076] Fix missing parameter in UI workflow by @ncovercash in #50

## [1.4.0](https://github.com/folio-org/.github/tree/v1.4.0) (2024-09-20)
[Full Changelog](https://github.com/folio-org/.github/compare/v1.3.0...v1.4.0)

* [FOLIO-3994] Initial Go SonarCloud by @dcrossleyau in #48
* [FOLIO-4076] Support custom GA yarn.lock retention by @ncovercash in #49

## [1.3.0](https://github.com/folio-org/.github/tree/v1.3.0) (2024-09-18)
[Full Changelog](https://github.com/folio-org/.github/compare/v1.2.2...v1.3.0)

* FOLIO-3994 FOLIO-4039 Enable docker tag version always by @dcrossleyau in #44
* FOLIO-3994 Go update launchDescriptor which registry by @dcrossleyau in #45
* FOLIO-3994 Improve doc Go Workflows by @dcrossleyau in #46
* FOLIO-4070 Make sonarcloud CI task optional by @ncovercash in #47

## [1.2.2](https://github.com/folio-org/.github/tree/v1.2.2) (2024-09-12)
[Full Changelog](https://github.com/folio-org/.github/compare/v1.2.1...v1.2.2)

* Only download sonar coverage reports for enabled test suites by @ncovercash in #43

## [1.2.1](https://github.com/folio-org/.github/tree/v1.2.1) (2024-09-05)
[Full Changelog](https://github.com/folio-org/.github/compare/v1.2.0...v1.2.1)

* FOLIO-4039 Initial docker workflows by @dcrossleyau in #38
* Use new CI number calculation by @ncovercash in #39

## [1.2.0](https://github.com/folio-org/.github/tree/v1.2.0) (2024-08-28)
[Full Changelog](https://github.com/folio-org/.github/compare/v1.1.0...v1.2.0)

* Require module descriptor generation (generate-module-descriptor) for publishing (publish-module-descriptor)
* All dependency installation before publish (install-before-publish)

## [1.1.0](https://github.com/folio-org/.github/tree/v1.1.0) (2024-05-22)
[Full Changelog](https://github.com/folio-org/.github/compare/v1.0.1...v1.1.0)

* bump node-version from v18.x to v20.x. Refs FOLIO-4007.

## [1.0.1](https://github.com/folio-org/.github/tree/v1.0.1) (2024-05-03)
[Full Changelog](https://github.com/folio-org/.github/compare/v1.0.0...v1.0.1)

* Fixes error where tags would not deploy when not on a master/main branch

## [1.0.0](https://github.com/folio-org/.github/tree/v1.0.0) (2024-04-22)

* Initial reusable UI workflow
