---
name: powershell-7-6-plus
description: Use when writing, reviewing, debugging, explaining, or running PowerShell commands, .ps1 scripts, or Windows terminal automation; when targeting PowerShell 7 (`pwsh`) or Windows PowerShell 5.1 (`powershell.exe`); when translating Bash/Linux commands; or when quoting, paths, env vars, profiles, `$PROFILE`, `$PSHOME`, local `.\` execution, JSON, curl/web requests, native executables, `$PSNativeCommandArgumentPassing`, `$LASTEXITCODE`, remoting, WinRM, SSH remoting, WMI/CIM migration, install/update workflows, `PSScriptAnalyzer`, `Install-Module`, or `Install-PSResource` may differ by shell or PowerShell version.
---

# PowerShell 7.6+ Guidance

Use this skill for any Windows shell task that might be PowerShell-specific. Load it before giving commands, not after a command already failed.

## 1. When to use this skill

- Use it any time you write PowerShell commands.
- Use it any time the user is on Windows and the shell is not explicitly Bash.
- Use it any time Bash/Linux docs are being translated into PowerShell.
- Use it any time `pwsh`, `powershell.exe`, `.ps1`, `npm`, `node`, `python`, `py`, `git`, `curl`, JSON, environment variables, profiles, remoting, or quoting are involved.

## 2. First check: which shell?

- State the target shell before giving commands:
  - `PowerShell 7 (pwsh)`
  - `Windows PowerShell 5.1 (powershell.exe)`
  - `cmd.exe`
  - `bash`
- Prefer `PowerShell 7 (pwsh)` for modern guidance.
- Treat `Windows PowerShell 5.1` as a separate legacy shell. Do not blur it together with `pwsh`.
- Never assume Bash syntax works in PowerShell.
- If a command only works in one shell or one PowerShell version, say so explicitly.

## 3. PowerShell 7.6+ vs Windows PowerShell 5.1

- `pwsh` is modern, cross-platform, side-by-side, and the default choice for new scripts.
- `powershell.exe` is the legacy Windows-inbox shell.
- PowerShell 7 installs side-by-side with 5.1. Do not say it replaces or uninstalls 5.1.
- Some older Windows modules still require 5.1 or `Import-Module -UseWindowsPowerShell` on Windows.
- If compatibility is uncertain, verify first:

```powershell
$PSVersionTable
$PSEdition
$PSHOME
```

## 4. Command style rules

- Prefer full cmdlet names in scripts, documentation, and reusable automation.
- Aliases are acceptable for interactive examples only when clarity is not harmed.
- Prefer object pipelines over text parsing when objects are available.
- Use these discovery tools early:
  - `Get-Command`
  - `Get-Help`
  - `Update-Help`
  - `Get-Member`
- Prefer these pipeline tools over ad hoc string work:
  - `Where-Object`
  - `ForEach-Object`
  - `Select-Object`
  - `Sort-Object`
  - `Group-Object`
  - `Measure-Object`
- Keep `Format-Table` and `Format-List` for final display only. Do not return formatted text from reusable functions.

## 5. Native command rules

- Native executables are not cmdlets. Examples: `git`, `npm`, `node`, `python`, `py`, `gh`, `docker`, `adb`, `curl.exe`.
- Resolve what a command actually is before assuming behavior:

```powershell
Get-Command git, npm, python, py, curl -All
```

- Do not use Bash line continuation `\`.
- Prefer a single line or multiple separate commands. Use the PowerShell backtick only when unavoidable.
- Do not use Bash env syntax like `VAR=value command`.
- Use process-scoped environment variables instead:

```powershell
$env:NODE_ENV = 'production'
npm run build
```

- For one-shot scoped execution, prefer:

```powershell
& {
    $env:NODE_ENV = 'production'
    npm run build
}
```

- Do not use `&&` unless you intentionally target PowerShell 7+. For cross-version guidance, prefer separate steps or explicit exit checks.
- For HTTP:
  - Prefer `Invoke-RestMethod` for JSON APIs.
  - Prefer `Invoke-WebRequest` for PowerShell-native web downloads and response objects.
  - Use `curl.exe` only when exact curl behavior is required.
- Never assume `curl` means the same thing on every Windows machine or PowerShell version.

## 6. Native executable edge cases

- In PowerShell 7.3+, native argument passing is controlled by `$PSNativeCommandArgumentPassing`.
- On Windows, argument passing can differ between PowerShell 5.1, PowerShell 7.2, and PowerShell 7.3+.
- `$PSNativeCommandUseErrorActionPreference` affects whether native stderr can participate in PowerShell error handling. Do not assume it is set the way you want.
- Use `$LASTEXITCODE` for native executable exit codes.
- Do not rely only on `$?` for native tools in mixed 5.1/7 guidance.
- PowerShell does not run current-directory executables by bare name. Use `.\tool.exe`, `.\script.ps1`, `.\gradlew`, or `.\gradlew.bat`.
- Use `Start-Process` when launching installers, opening files, or needing `-Verb RunAs` for elevation.
- Do not fake elevation inside a normal shell. Clearly say when Administrator PowerShell is required.
- For complex native quoting, prefer correct PowerShell quoting first. Use `--%` only as a Windows-only last resort.

## 7. Quoting and paths

- Use single quotes for literal strings.
- Use double quotes only when interpolation is needed.
- Quote any path that contains spaces.
- Prefer `Join-Path` in scripts over hand-built path strings.
- Prefer `-LiteralPath` when a path may contain wildcard characters like `[` or `]`.
- Use `Test-Path` and `Resolve-Path` before destructive or install actions.
- Do not blindly escape like Bash.

## 8. JSON, web, and file output

- Build objects first, then serialize them. Do not hand-concatenate JSON strings when objects will do.
- Use `ConvertTo-Json -Depth N` deliberately. The default depth is too shallow for many real payloads.
- Use `ConvertFrom-Json` for JSON input.
- Prefer `Get-Content -Raw | ConvertFrom-Json` for file-backed JSON.
- Be explicit with `-Encoding utf8` when writing files consumed by other tools.
- Do not assume `>` / `Out-File` / `Set-Content` / `Add-Content` behave exactly like Bash redirection.
- Use `Tee-Object` when output must be displayed and saved.
- Use `Start-Transcript` for session logs.
- Be careful with stream redirection like `*>` and `2>&1`; PowerShell streams are not Bash streams.

## 9. PowerShell 7 migration traps

- Prefer `Get-CimInstance` over legacy or removed WMI v1 cmdlets like `Get-WmiObject`.
- Prefer `Get-WinEvent` over older `Get-EventLog` patterns when targeting PowerShell 7.
- Do not use `Add-PSSnapin`, `#requires -PSSnapin`, or PowerShell Workflow for new PowerShell 7 scripts.
- Use `Import-Module -UseWindowsPowerShell` only as a Windows compatibility bridge, not as proof that a module is truly PowerShell 7-native.
- Remember that PowerShell 7 profile and module paths differ from Windows PowerShell 5.1 paths.

## 10. Install, update, and environment checks

- For installing or updating PowerShell on Windows:
  - prefer `winget` for normal clients
  - prefer MSI for servers and enterprise deployment
  - use MSIX or Microsoft Store only when their sandbox limits are acceptable
  - use ZIP for side-by-side or manual installs
- Update PowerShell using the same method used to install it unless Microsoft Update is already managing it.
- For modules and packages, know the difference between:
  - `PowerShellGet` / `Install-Module`
  - `PSResourceGet` / `Install-PSResource`
- Use these checks before guessing the environment:

```powershell
$PSVersionTable
$PSEdition
$PSHOME
Get-Command <name> -All
$env:PSModulePath -split [IO.Path]::PathSeparator
```

## 11. Profiles, startup, and remoting

- PowerShell 7 profile paths differ from Windows PowerShell 5.1.
- Use `$PROFILE | Select-Object *` to inspect startup profile paths.
- Use `pwsh -NoProfile` to debug slow or broken startup.
- For clean script runs, prefer `pwsh -NoProfile -File .\script.ps1`.
- For temporary policy bypass on one run, prefer `pwsh -NoProfile -ExecutionPolicy Bypass -File .\script.ps1`.
- Do not recommend changing `LocalMachine` execution policy for one script.
- For Windows-to-Windows admin remoting, understand `WinRM` / `WSMan`.
- For cross-platform remoting, PowerShell 7 supports SSH remoting.
- Do not suggest `Enable-PSRemoting`, `Enter-PSSession`, `New-PSSession`, or `Invoke-Command` without mentioning setup, trust, and elevation requirements.
- Do not use Bash-style backgrounding assumptions.
- For PowerShell background work, use `Start-Job`, `Receive-Job`, and `Remove-Job`, or explain why a native tool should be run separately.

## 12. Safety rules

- Never suggest destructive commands without explaining the target and the risk.
- Never suggest `Remove-Item -Recurse -Force` against a broad or computed path without first showing the resolved target.
- Prefer dry runs first:

```powershell
Remove-Item -LiteralPath $target -Recurse -Force -WhatIf
```

- Prefer `-WhatIf` and `-Confirm` where available.
- Execution policy is **not** a security boundary.
- Do not recommend weakening execution policy globally unless the user explicitly understands the risk and scope.
- Never default to `irm ... | iex`.
- For downloaded scripts or archives, prefer download -> hash or source check -> inspect -> `Unblock-File` if needed -> run.
- Prefer `Unblock-File` for trusted downloaded scripts blocked by Mark-of-the-Web instead of weakening execution policy globally.
- Use `Get-FileHash`, `Expand-Archive`, `Test-Path`, and `Resolve-Path` in reviewable download/install flows.
- Do not hide errors with `-ErrorAction SilentlyContinue` unless there is a specific, justified reason.

## 13. Script quality

- In automation scripts, prefer:

```powershell
Set-StrictMode -Version Latest
$ErrorActionPreference = 'Stop'
```

- Use `try/catch` when failure should stop or be reported clearly.
- Use `Get-Verb` for approved verbs.
- Use `[CmdletBinding()]`, typed `param()` blocks, and validation attributes like `ValidateSet` and `ValidateNotNullOrEmpty`.
- Use `ValueFromPipeline` only when pipeline input is intentionally supported.
- For destructive functions, use `SupportsShouldProcess`.
- Return objects from functions, not display formatting.
- For reusable scripts and modules, recommend `PSScriptAnalyzer` and `Invoke-ScriptAnalyzer`.

## 14. Tooling

- Windows Terminal is a terminal host, not PowerShell.
- VS Code with the PowerShell extension is the modern scripting environment.
- Windows PowerShell ISE is legacy and only targets Windows PowerShell 5.1.

## 15. AI mistake checklist

Before giving a PowerShell answer, check for these failures:

- Bash syntax pasted into PowerShell
- `cmd.exe`, PowerShell 5.1, and PowerShell 7 confused together
- Linux path assumptions on Windows
- Text parsing where object properties exist
- Aliases used in durable scripts
- `curl` treated as guaranteed real curl
- JSON serialized without enough `-Depth`
- Dangerous delete or move commands without safety checks
- Native command success checked loosely instead of with `$LASTEXITCODE`
- Current-directory executables invoked without `.\`
- Outdated Windows PowerShell-only assumptions applied to PowerShell 7

## 16. Output style for future agents

- State the target shell first.
- Prefer commands that can be copied safely as written.
- For multi-step workflows, explain each step briefly.
- For risky commands, provide a dry run first.
- If translating from Bash docs, say that the command was translated for PowerShell.
- If compatibility matters, provide separate `pwsh` and `powershell.exe` variants instead of pretending one command fits both.
