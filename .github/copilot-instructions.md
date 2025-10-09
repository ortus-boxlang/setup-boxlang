# Copilot Instructions for setup-boxlang

Welcome to the `setup-boxlang` GitHub Action codebase! This project provides a composite GitHub Action to install and configure the BoxLang Dynamic JVM Language runtime for CI workflows. Use these guidelines to maximize your productivity as an AI coding agent in this repository.

## Project Overview
- **Purpose:** Automate the setup of BoxLang (https://boxlang.io) in GitHub Actions workflows, optionally with CommandBox and custom modules.
- **Key Files:**
  - `action.yml`: Main action definition, input/output schema, and composite steps.
  - `README.md`: Usage documentation, input descriptions, and example workflows.
- **Architecture:**
  - Composite action using Bash scripts to download and install BoxLang and optional modules.
  - Relies on the Temurin JDK 21 via `actions/setup-java@v4`.
  - Supports optional installation of CommandBox and user-specified BoxLang modules.

## Critical Workflows
- **Installation:**
  - Downloads and runs `install-boxlang.sh` from Ortus Solutions.
  - Installs BoxLang to `$HOME/.boxlang` and exposes `BOXLANG_HOME`.
  - Verifies installation with `boxlang --version`.
- **Module Installation:**
  - If `modules` input is provided, runs `install-bx-module` for each module.
- **Outputs:**
  - `boxlang-version` and `installation-path` are set as outputs for downstream steps.

## Project-Specific Conventions
- **Inputs:**
  - `with-commandbox`: Boolean, installs CommandBox if true.
  - `modules`: Space-delimited list of modules to install.
  - `version`: BoxLang version (default: latest).
- **Error Handling:**
  - All critical steps exit on failure with clear error messages.
- **Environment:**
  - Requires Java 21+ (Temurin JDK).
  - Exports `BOXLANG_HOME` to the environment for subsequent steps.

## Integration Points
- **External Dependencies:**
  - Downloads installer from Ortus Solutions.
  - Uses `actions/setup-java@v4` for JDK setup.
- **Cross-Component Communication:**
  - Uses GitHub Actions environment variables and outputs for step-to-step data flow.

## Examples
- See `README.md` for YAML workflow usage patterns and input combinations.
- Refer to `action.yml` for the full installation and module setup logic.

## Tips for AI Agents
- Always check `action.yml` for the latest supported inputs and logic.
- When updating installation logic, ensure error handling and output setting are robust.
- Keep documentation in `README.md` and this file in sync with action behavior.

---
For questions about BoxLang, see https://boxlang.io or the Ortus Solutions documentation.
