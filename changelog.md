# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

----

## [Unreleased]

### Added

- New input: `boxlang-home` to allow users to customize the BoxLang installation directory. Defaults to `${GITHUB_WORKSPACE}/.boxlang` to avoid read-only filesystem issues with `/home/runner` on GitHub Actions.

### Changed

- BOXLANG_HOME environment variable is now set before installation to ensure BoxLang installs to a writable location. This resolves issues with GitHub Actions runners where `/home/runner` is read-only.

### Security

- Added explicit `permissions: contents: read` to test workflow to follow the principle of least privilege and address CodeQL security alerts.

## [1.2.0] - 2025-10-09

### Added

- New inputs: `commandbox_version` and `commandbox_modules` to specify the version of CommandBox and any modules to install.
- New input: `forgeboxAPIKey` to configure the ForgeBox API Key in CommandBox for authenticated package access.

----

## [1.1.0] - 2025-07-07

### Fixed

- The new location of the installer as we have now moved to a new repository.

----

## [1.0.0] - 2025-06-04

- First release of the project.
