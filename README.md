# Setup BoxLang Action

This action sets up the BoxLang Dynamic JVM Language runtime (www.boxlang.io)

## Inputs

The following are all the different input variables you can use on the action so you can setup BoxLang in your GitHub Actions workflow.

| Input                    | Type          | Default       | Description |
| ------------------------ | ------------- | ------------- | ----------- |
| `modules`              | string        | ---           | If added, a space-delimited list of modules to install upon installation of the binary for you. |
| `version`                | semver        | `latest`      | The BoxLang version to install, if not passed we use the latest stable. |
| `skip-package-install`   | boolean       | `false`       | Skip installation of system packages (curl, zip, unzip, jq, openjdk-21-jre). Useful if packages are already installed. |

## Usage

Simple usage:

```yaml
- name: Setup BoxLang
  uses: ortus-boxlang/setup-boxlang@v1
```

With Specific Modules:

```yaml
- name: Setup BoxLang
  uses: ortus-boxlang/setup-boxlang@v1
  with:
    modules: bx-ai bx-orm bx-pdf
```

Install a specific version of BoxLang:

```yaml
- name: Setup BoxLang with specific version
  uses: ortus-boxlang/setup-boxlang@v1
  with:
    version: snapshot
```

Here is another one:

```yaml
- name: Setup BoxLang with specific version
  uses: ortus-boxlang/setup-boxlang@v1
  with:
    version: 1.1.0
```

Skip system package installation (if packages are already available):

```yaml
- name: Setup BoxLang without installing system packages
  uses: ortus-boxlang/setup-boxlang@v1
  with:
    skip-package-install: true
    modules: bx-ai bx-orm
```

## System Requirements

This action will automatically install the following system packages if they are not already available:
- `curl` - For downloading the installer
- `zip` and `unzip` - For archive handling
- `jq` - For JSON processing
- `openjdk-21-jre` (or equivalent) - Java Runtime Environment for BoxLang

The action supports multiple package managers:
- **Ubuntu/Debian**: `apt-get`
- **RHEL/CentOS/Amazon Linux**: `yum`
- **Fedora**: `dnf`
- **macOS**: `brew`

If you're using a runner with these packages pre-installed, you can skip the installation step by setting `skip-package-install: true`.
