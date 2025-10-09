# Setup BoxLang Action

This action sets up the BoxLang Dynamic JVM Language runtime (www.boxlang.io)

## Inputs

The following are all the different input variables you can use on the action so you can setup BoxLang in your GitHub Actions workflow.

| Input                    | Type          | Default       | Description |
| ------------------------ | ------------- | ------------- | ----------- |
| `with-commandbox` | boolean       | `false`       | If true, it will install the latest CommandBox as well. |
| `commandbox_version` | string       | `latest`       | The CommandBox version to install. Only used if `with-commandbox` is true. |
| `commandbox_modules` | string       | ---           | If added, a comma-delimited list of CommandBox packages to install. Only used if `with-commandbox` is true. |
| `forgeboxAPIKey` | string       | ---           | If added, it will configure the ForgeBox API Key in CommandBox. Only used if `with-commandbox` is true. |
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

Install BoxLang with a specific CommandBox version:

```yaml
- name: Setup BoxLang with specific CommandBox version
  uses: ortus-boxlang/setup-boxlang@1.1.0
  with:
    with-commandbox: true
    commandbox_version: 6.0.0
```

Install BoxLang with CommandBox and modules:

```yaml
- name: Setup BoxLang with CommandBox and modules
  uses: ortus-boxlang/setup-boxlang@1.1.0
  with:
    with-commandbox: true
    commandbox_modules: commandbox-cfconfig,commandbox-dotenv
```

Install BoxLang with CommandBox (specific version) and modules:

```yaml
- name: Setup BoxLang with CommandBox (full setup)
  uses: ortus-boxlang/setup-boxlang@1.1.0
  with:
    with-commandbox: true
    commandbox_version: 6.0.0
    commandbox_modules: commandbox-cfconfig,commandbox-dotenv,commandbox-fusionreactor
```

Install BoxLang with CommandBox and ForgeBox API Key:

```yaml
- name: Setup BoxLang with CommandBox and ForgeBox API Key
  uses: ortus-boxlang/setup-boxlang@1.1.0
  with:
    with-commandbox: true
    forgeboxAPIKey: ${{ secrets.FORGEBOX_API_KEY }}
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
