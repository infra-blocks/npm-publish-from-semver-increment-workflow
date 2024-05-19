# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.2] - 2024-05-19

### Fixed

- Skip `post-comment` types of actions when the homologous workflow is `skipped`. Because we now use the
`skip` input in favor of an `if` clause, we lost the ability to automatically skip post comment actions. This is now
achieved by inlining the same condition that would skip the matching release workflow.

## [1.0.1] - 2024-05-18

### Changed

- Migrated to the [infra-blocks](https://github.com/infra-blocks) organization.

## [1.0.0] - 2024-04-20

### Added

- First release!

[1.0.2]: https://github.com/infra-blocks/npm-publish-from-semver-increment-workflow/compare/v1.0.1...v1.0.2
[1.0.1]: https://github.com/infra-blocks/npm-publish-from-semver-increment-workflow/compare/v1.0.0...v1.0.1
[1.0.0]: https://github.com/infra-blocks/npm-publish-from-semver-increment-workflow/releases/tag/v1.0.0
