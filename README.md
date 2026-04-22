# ⚡ Setup BoxLang GitHub Action

[![Latest Release](https://img.shields.io/github/v/release/ortus-boxlang/setup-boxlang?color=blue)](https://github.com/ortus-boxlang/setup-boxlang/releases)
[![License](https://img.shields.io/badge/license-Apache_2.0-green.svg)](LICENSE)
![Platform Support](https://img.shields.io/badge/platform-Linux%20%7C%20macOS%20%7C%20Windows-brightgreen)

🚀 **Automate your BoxLang setup** - This GitHub Action sets up the [BoxLang Dynamic JVM Language](https://boxlang.io) runtime for CI/CD workflows with optional CommandBox CLI and module installation. Supports **Linux, macOS, and Windows** runners.

## 📋 Inputs

Configure your BoxLang setup using these input parameters:

| Input                    | Type          | Default       | Description |
| ------------------------ | ------------- | ------------- | ----------- |
| `boxlang-home` | string       | `${GITHUB_WORKSPACE}/.boxlang`       | Custom directory for BoxLang installation. Defaults to a writable directory in the workspace to avoid read-only filesystem issues. |
| `with-commandbox` | boolean       | `false`       | If true, it will install the latest CommandBox as well. |
| `commandbox_version` | string       | `latest`       | The CommandBox version to install. Only used if `with-commandbox` is true. |
| `commandbox_modules` | string       | ---           | If added, a comma-delimited list of CommandBox packages to install. Only used if `with-commandbox` is true. |
| `forgeboxAPIKey` | string       | ---           | If added, it will configure the ForgeBox API Key in CommandBox. Only used if `with-commandbox` is true. |
| `modules`              | string        | ---           | If added, a space-delimited list of modules to install upon installation of the binary for you. |
| `version`                | semver        | `latest`      | The BoxLang version to install, if not passed we use the latest stable. |

## 🚀 Usage

### Simple Setup

```yaml
- name: Setup BoxLang
  uses: ortus-boxlang/setup-boxlang@main
```

### 📦 With BoxLang Modules

```yaml
- name: Setup BoxLang
  uses: ortus-boxlang/setup-boxlang@main
  with:
    modules: bx-ai bx-orm bx-pdf
```

### 🎯 Specific Version

```yaml
- name: Setup BoxLang with specific version
  uses: ortus-boxlang/setup-boxlang@main
  with:
    version: snapshot
```

### �️ Multi-OS Matrix (Linux, macOS, Windows)

```yaml
jobs:
  test:
    strategy:
      matrix:
        os: [ubuntu-latest, macos-latest, windows-latest]
    runs-on: ${{ matrix.os }}
    steps:
      - name: Setup BoxLang
        uses: ortus-boxlang/setup-boxlang@main

      - name: Verify BoxLang
        run: boxlang --version
```

### �📁 Custom Installation Directory

```yaml
- name: Setup BoxLang with custom installation directory
  uses: ortus-boxlang/setup-boxlang@main
  with:
    boxlang-home: /tmp/boxlang
```

### 📦 With CommandBox

```yaml
- name: Setup BoxLang with CommandBox
  uses: ortus-boxlang/setup-boxlang@main
    with:
        with-commandbox: true
```

### 🔧 CommandBox with Specific Version

```yaml
- name: Setup BoxLang with specific CommandBox version
  uses: ortus-boxlang/setup-boxlang@main
  with:
    with-commandbox: true
    commandbox_version: 6.0.0
```

### 🛠️ CommandBox with Modules

```yaml
- name: Setup BoxLang with CommandBox and modules
  uses: ortus-boxlang/setup-boxlang@main
  with:
    with-commandbox: true
    commandbox_modules: commandbox-cfconfig,commandbox-dotenv
```

### ⚙️ Full Configuration Example

```yaml
- name: Setup BoxLang with CommandBox (full setup)
  uses: ortus-boxlang/setup-boxlang@main
  with:
    with-commandbox: true
    commandbox_version: 6.0.0
    commandbox_modules: commandbox-cfconfig,commandbox-dotenv,commandbox-fusionreactor
```

Install BoxLang with CommandBox and ForgeBox API Key:

```yaml
- name: Setup BoxLang with CommandBox and ForgeBox API Key
  uses: ortus-boxlang/setup-boxlang@main
  with:
    with-commandbox: true
    forgeboxAPIKey: ${{ secrets.FORGEBOX_API_KEY }}
```

Here is another one:

```yaml
- name: Setup BoxLang with specific version
  uses: ortus-boxlang/setup-boxlang@main
  with:
    version: main
```


## 📦 Action Outputs

This action provides the following outputs for use in subsequent workflow steps:

- `boxlang-version`: The version of BoxLang that was installed
- `installation-path`: The path where BoxLang was installed

## 🚀 About BoxLang

**BoxLang** is a modern dynamic JVM language that can be deployed on multiple runtimes: operating system (Windows/Mac/*nix/Embedded), web server, lambda, iOS, Android, web assembly, and more. BoxLang combines many features from different programming languages, including Java, CFML, Python, Ruby, Go, and PHP, to provide developers with a modern and expressive syntax.

> **Platform Support:** This action uses the official Ortus Solutions bash installer for Linux/macOS and the official PowerShell installer for Windows, providing native support across all major GitHub Actions runner platforms.

### ✨ Why BoxLang?

- 🏎️ **Rapid Application Development (RAD)**
- 🔄 **Dynamic, modular, and lightweight**
- ☕ **100% Java & JVM-friendly**
- 🎯 **Multi-runtime: CLI, Web, Lambda, Mobile, and more**
- 🔥 **Not only a language but a framework**

### 📚 Resources

- 🌐 [Website](https://boxlang.io)
- 🧑‍💻 [Try BoxLang Online](https://try.boxlang.io)
- 📖 [Documentation](https://boxlang.ortusbooks.com)
- 🎥 [YouTube Channel](https://www.youtube.com/c/ortussolutions)
- 🎓 [BoxLang Academy](https://academy.boxlang.io)
- 💻 [GitHub Organization](https://github.com/ortus-boxlang)
- 💬 [Community Slack](https://boxteam.ortussolutions.com)
- 💬 [Community Forums](https://community.ortussolutions.com)
- 🕸️ [Module Registry](https://forgebox.io/)
- 🐦 [Twitter](https://x.com/TryBoxLang)
- 👔 [LinkedIn](https://www.linkedin.com/company/tryboxlang/)
- 📘 [Facebook](https://www.facebook.com/TryBoxLang)
- 🫶 [Support Us](https://www.patreon.com/ortussolutions)

---

Made with ❤️ by [Ortus Solutions](https://www.ortussolutions.com)
