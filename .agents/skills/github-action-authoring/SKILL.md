---
name: github-action-authoring
description: 'Authoring and debugging composite GitHub Actions for setup-boxlang. Use when: adding platform support (Windows/Linux/macOS), fixing PATH issues on Windows runners, updating action inputs/outputs/steps, writing PowerShell steps, debugging installer flag ordering, or adding CI test jobs to tests.yml.'
---

# GitHub Action Authoring — setup-boxlang

## When to Use
- Adding or fixing Windows, Linux, or macOS support in `action.yml`
- Writing or debugging PowerShell (`shell: powershell`) steps
- Adding new inputs, outputs, or conditional steps
- Updating `tests.yml` with new platform/feature coverage
- Debugging PATH issues on GitHub Actions Windows runners
- Installing third-party tools (BoxLang, CommandBox) in CI

---

## Architecture

`action.yml` is a **composite action** with platform-specific parallel step pairs:

| Step | Unix/Linux/macOS | Windows |
|------|-----------------|---------|
| Setup BoxLang | `shell: bash` + `install-boxlang.sh` | `shell: powershell` + `install-boxlang.ps1` |
| Install CommandBox | `shell: bash` + `curl` + `unzip` | `shell: powershell` + dedicated step |
| Install modules | `shell: bash` + `install-bx-module` | skipped (not available on Windows) |
| ForgeBox API Key | `shell: bash` | `shell: powershell` |

Every step pair uses `if: runner.os != 'Windows'` / `if: runner.os == 'Windows'` conditions.

---

## Critical Rules

### Platform conditionals
Always use **both** sides for every platform-specific step:
```yaml
- name: My Step (Unix/Linux/macOS)
  if: runner.os != 'Windows'
  shell: bash
  run: |
    ...

- name: My Step (Windows)
  if: runner.os == 'Windows'
  shell: powershell
  run: |
    ...
```

### Environment variables in PowerShell steps
`${{ env.SOME_VAR }}` does **NOT** expand inside composite action PowerShell step bodies — it resolves to an empty string. Always use `$env:SOME_VAR` to read runner environment variables at runtime:
```powershell
# WRONG — ${{ env.GITHUB_WORKSPACE }} expands to empty in PS steps
$dir = "${{ env.GITHUB_WORKSPACE }}\.boxlang"

# CORRECT
$dir = "$env:GITHUB_WORKSPACE\.boxlang"
```
`${{ inputs.xxx }}` and `${{ runner.os }}` DO expand correctly (they are template substitutions, not env reads).

### Writing to GITHUB_ENV and GITHUB_OUTPUT in PowerShell
```powershell
# Set env var for subsequent steps
Add-Content -Path $env:GITHUB_ENV -Value "MY_VAR=value"

# Set step output
Add-Content -Path $env:GITHUB_OUTPUT -Value "my-output=value"

# Update PATH for subsequent steps
$env:PATH = "C:\my\bin;$env:PATH"
Add-Content -Path $env:GITHUB_ENV -Value "PATH=$env:PATH"
```

### Invoke-WebRequest Content is byte[] in PS7+
On `windows-latest` (PowerShell 7), `.Content` from `Invoke-WebRequest` is `System.Byte[]`, not a string. `Invoke-Expression` fails on it. Always save to a file first:
```powershell
# WRONG — fails on PS7+ runners
$script = Invoke-WebRequest -Uri $url -UseBasicParsing
Invoke-Expression $script.Content

# CORRECT
$tmpScript = [System.IO.Path]::Combine([System.IO.Path]::GetTempPath(), "script.ps1")
Invoke-WebRequest -Uri $url -OutFile $tmpScript -UseBasicParsing -ErrorAction Stop
& $tmpScript <arguments>
Remove-Item $tmpScript -Force -ErrorAction SilentlyContinue
```

### Installer flag ordering matters
The `install-boxlang.ps1` installer parses flags sequentially. `--yes` sets `INSTALL_COMMANDBOX=true` internally. If `--without-commandbox` comes **before** `--yes`, that later `--yes` overrides it. Always put `--yes` first:
```powershell
# WRONG — --yes overrides --without-commandbox
& $tmpScript --without-commandbox --yes

# CORRECT — --without-commandbox processed last, wins
& $tmpScript --yes --without-commandbox
```

### Windows PATH after installer
Third-party installers on Windows write to the Machine registry PATH (requires admin). Even if the registry write succeeds, the **current PowerShell session does not pick it up automatically**. Always explicitly rebuild PATH and push it to `GITHUB_ENV`:
```powershell
# Explicitly prepend known install paths — don't rely on the installer having done it
$env:PATH = "C:\known\install\bin;" +
            [System.Environment]::GetEnvironmentVariable("PATH", "Machine") + ";" +
            [System.Environment]::GetEnvironmentVariable("PATH", "User")
Add-Content -Path $env:GITHUB_ENV -Value "PATH=$env:PATH"
```

### Never delegate CommandBox installation to the BoxLang PS1 installer on Windows
The BoxLang installer places `box.exe` at `c:\boxlang\bin\box.exe` but its PATH update is unreliable on CI. Use the dedicated **"Install CommandBox (Windows)"** step instead, which:
1. Downloads `type/windows` from Ortus (not `type/bin` — that is the Linux binary)
2. Extracts to a controlled path (`C:\ProgramData\CommandBox`)
3. Verifies with the full path `& "$boxDir\box.exe" version` — no PATH trust needed
4. Explicitly prepends `$boxDir` to PATH + GITHUB_ENV

### Composite action outputs when steps are conditional
Each platform step must have its own `id`. Outputs must pick the right step per platform:
```yaml
outputs:
  boxlang-version:
    value: ${{ runner.os == 'Windows' && steps.install-boxlang-windows.outputs.version || steps.install-boxlang.outputs.version }}
```

---

## BoxLang Installer Reference

| Platform | Installer URL | Version control |
|----------|--------------|-----------------|
| Unix/Linux/macOS | `https://downloads.ortussolutions.com/ortussolutions/boxlang-quick-installer/install-boxlang.sh` | Positional arg: `./install-boxlang.sh 1.2.0 --without-commandbox` |
| Windows | `https://downloads.ortussolutions.com/ortussolutions/boxlang-quick-installer/install-boxlang.ps1` | `$env:BOXLANG_TARGET_VERSION = "1.2.0"` before calling script |

Unix installer flags: `<version> --without-commandbox`
Windows installer flags: `--yes --without-commandbox` (or `--yes --with-commandbox`)

### CommandBox Download URLs

| Platform | URL |
|----------|-----|
| Linux/macOS | `https://www.ortussolutions.com/parent/download/commandbox/type/bin` |
| Windows | `https://www.ortussolutions.com/parent/download/commandbox/type/windows` |
| Specific version | `https://downloads.ortussolutions.com/ortussolutions/commandbox/<version>/commandbox-bin-<version>.zip` |

---

## Bash → PowerShell Conversion Quick Reference

| Bash | PowerShell |
|------|-----------|
| `if [[ -n "$VAR" ]]` | `if ("$VAR" -ne "")` |
| `if [[ "$A" == "$B" ]]` | `if ("$A" -eq "$B")` |
| `mkdir -p dir` | `New-Item -ItemType Directory -Path dir -Force \| Out-Null` |
| `echo "msg"` | `Write-Host "msg"` |
| `echo "K=V" >> $GITHUB_ENV` | `Add-Content -Path $env:GITHUB_ENV -Value "K=V"` |
| `echo "k=v" >> $GITHUB_OUTPUT` | `Add-Content -Path $env:GITHUB_OUTPUT -Value "k=v"` |
| `curl -fsSL url -o file` | `Invoke-WebRequest -Uri url -OutFile file -UseBasicParsing` |
| `unzip file -d dir` | `Expand-Archive -Path file -DestinationPath dir -Force` |
| `which cmd` | `(Get-Command cmd -ErrorAction SilentlyContinue).Source` |
| `export VAR=val` | `$env:VAR = "val"` |
| `exit 1` | `exit 1` |
| `cmd 2>&1` | `cmd 2>&1` |
| `$?` (exit code) | `$LASTEXITCODE` |

---

## tests.yml Conventions

- Each new **feature** gets its own job (e.g. `test_with_modules`)
- Each new **platform** gets its own named job (e.g. `test_windows_default_home`)
- Platform matrix jobs use `strategy.matrix.os` and `runs-on: ${{ matrix.os }}`
- Windows verification steps use `shell: powershell` and check env vars with `$env:VAR`
- Unix verification steps use inline `bash` `run:` blocks
- All Windows jobs verify `BOXLANG_HOME` is set and contains the expected path

```yaml
# Windows env var verification pattern
- name: Verify BOXLANG_HOME (Windows)
  shell: powershell
  run: |
    if ($env:BOXLANG_HOME -ne "expected\path") {
      Write-Host "Expected: expected\path  Got: $env:BOXLANG_HOME"
      exit 1
    }
    Write-Host "BOXLANG_HOME is correct: $env:BOXLANG_HOME"
```
