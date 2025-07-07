# Setup BoxLang Action

This action sets up the BoxLang Dynamic JVM Language runtime (www.boxlang.io)

## Inputs

The following are all the different input variables you can use on the action so you can setup BoxLang in your GitHub Actions workflow.

| Input                    | Type          | Default       | Description |
| ------------------------ | ------------- | ------------- | ----------- |
| `with-commandbox` | boolean       | `false`       | If true, it will install the latest CommandBox as well. |
| `modules`              | string        | ---           | If added, a space-delimited list of modules to install upon installation of the binary for you. |
| `version`                | semver        | `latest`      | The BoxLang version to install, if not passed we use the latest stable. |

## Usage

Simple usage:

```yaml
- name: Setup BoxLang
  uses: ortus-boxlang/setup-boxlang@1.1.0
```

With Specific Modules:

```yaml
- name: Setup BoxLang
  uses: ortus-boxlang/setup-boxlang@1.1.0
  with:
    modules: bx-ai bx-orm bx-pdf
```

Install a specific version of BoxLang:

```yaml
- name: Setup BoxLang with specific version
  uses: ortus-boxlang/setup-boxlang@1.1.0
  with:
    version: snapshot
```

Install BoxLang with CommandBox:

```yaml
- name: Setup BoxLang with CommandBox
  uses: ortus-boxlang/setup-boxlang@1.1.0
    with:
        with-commandbox: true
```

Here is another one:

```yaml
- name: Setup BoxLang with specific version
  uses: ortus-boxlang/setup-boxlang@1.1.0
  with:
    version: 1.1.0
```

## System Requirements

This action will automatically install the following system packages if they are not already available:

- `openjdk-21-jre` (or equivalent) - Java Runtime Environment for BoxLang
