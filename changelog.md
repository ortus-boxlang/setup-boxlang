# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

----

## [Unreleased]

----

## [1.5.1] - 2026-10-06

### Fixed

- `forgeboxAPIKey` now configures BoxLang-native CommandBox (`bx-cli`) on Linux, macOS, and Windows without requiring `with-commandbox: true`.
- Traditional CommandBox steps use its explicit executable path so a `bx-cli` launcher on `PATH` cannot intercept them when both CLIs are installed.
- Windows ForgeBox configuration now checks the native process exit code and fails if configuration fails.

### Security

- Pass ForgeBox tokens through environment variables and quoted arguments instead of interpolating them into shell scripts. Suppress token output with `--quiet` for both CLIs.

### Added

- Windows BoxLang module installation using the PowerShell module installer, with module launchers exposed through `${BOXLANG_HOME}/bin`.
- Authentication regression coverage for legacy and native CommandBox on Linux, macOS, and Windows, including coexistence and a custom Windows BoxLang home.
- Documented native CommandBox authentication and shared configuration. `with-commandbox` still installs the legacy standalone binary; migration to `bx-cli` remains a future change.

----

## [1.5.0] - 2026-08-24

### Fixed

- An executable a module installs (e.g. `bx-cli`'s own `box` script, via `install-bx-module`'s support for a module's `box.json` declaring `boxlang.executable`/`boxlang.executables`) is written to `${BOXLANG_HOME}/bin`, which was never added to the runner's `PATH`. Only `/usr/local/bin` (where the core `boxlang`/`bx` binaries are installed) was on `PATH`, so a module-provided executable like `box` was unreachable in every step after installation. `${BOXLANG_HOME}/bin` is now added to `$GITHUB_PATH` right after `BOXLANG_HOME` is set, before any modules are installed.

----

## [1.4.0] - 2026-04-22

### Added

- **Windows support**: The action now works on `windows-latest` GitHub Actions runners in addition to Linux and macOS.

### Changed

- All existing installation steps renamed with `(Unix/Linux/macOS)` suffix and made conditional (`runner.os != 'Windows'`).
- Action outputs now resolve correctly from the appropriate platform-specific step on both Windows and Unix runners.

----

## [1.3.0] - 2025-02-04

### Added

- New input: `boxlang-home` to allow users to customize the BoxLang installation directory. Defaults to `${GITHUB_WORKSPACE}/.boxlang` to avoid read-only filesystem issues with `/home/runner` on GitHub Actions.

### Changed

- BOXLANG_HOME environment variable is now set before installation to ensure BoxLang installs to a writable location. This resolves issues with GitHub Actions runners where `/home/runner` is read-only.

### Security

- Added explicit `permissions: contents: read` to test workflow to follow the principle of least privilege and address CodeQL security alerts.

----

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
