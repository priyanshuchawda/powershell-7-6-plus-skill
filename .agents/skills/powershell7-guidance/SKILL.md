---
name: powershell7-guidance
description: Use when writing, reviewing, debugging, explaining, or running PowerShell commands or .ps1 scripts, especially on Windows; when targeting PowerShell 7 (`pwsh`) or Windows PowerShell 5.1 (`powershell.exe`); when translating Bash/Linux commands into PowerShell; or when quoting, paths, env vars, JSON, curl/web requests, git/node/python/npm/docker commands, terminal automation, or native executable behavior might differ by shell.
---

# PowerShell 7 Guidance

Use this skill for any Windows shell task that might be PowerShell-specific. Load it before giving commands, not after a command already failed.

## 1. When to use this skill

- Use it any time you write PowerShell commands.
- Use it any time the user is on Windows and the shell is not explicitly Bash.
- Use it any time Bash/Linux docs are being translated into PowerShell.
- Use it any time `pwsh`, `powershell.exe`, `.ps1`, `npm`, `node`, `python`, `git`, `curl`, JSON, environment variables, paths, or quoting are involved.

## 2. First check: which shell?

- State the target shell before giving commands:
  - `PowerShell 7 (pwsh)`
  - `Windows PowerShell 5.1 (powershell.exe)`
  - `cmd.exe`
  - `bash`
- Prefer `PowerShell 7 (pwsh)` for modern PowerShell guidance.
- Treat `Windows PowerShell 5.1` as a separate legacy shell. Do not blur it together with `pwsh`.
- Never assume Bash syntax works in PowerShell.
- If the command only works in one shell, say so explicitly.

## 3. PowerShell 7 vs Windows PowerShell 5.1

- `pwsh` is modern, cross-platform, side-by-side, and the default choice for new scripts.
- `powershell.exe` is the legacy Windows-inbox shell.
- PowerShell 7 does **not** replace or uninstall Windows PowerShell 5.1.
- Some older Windows modules still require 5.1 or `Import-Module -UseWindowsPowerShell` on Windows.
- If compatibility is uncertain, tell the user to verify with:

```powershell
$PSVersionTable
$PSEdition
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
- Prefer these object-pipeline tools over ad hoc string work:
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
- For native executable success/failure, use `$LASTEXITCODE` when branching:

```powershell
npm run build
if ($LASTEXITCODE -ne 0) { throw 'Build failed' }
```

- For HTTP:
  - Prefer `Invoke-RestMethod` for JSON APIs.
  - Prefer `Invoke-WebRequest` for PowerShell-native web downloads/response objects.
  - Use `curl.exe` only when exact curl behavior is required.
- Never assume `curl` means the same thing on every Windows machine or PowerShell version.

## 6. Quoting and paths

- Use single quotes for literal strings.
- Use double quotes only when interpolation is needed.
- Quote any path that contains spaces.
- Prefer `Join-Path` in scripts over hand-built path strings.
- Prefer `-LiteralPath` when a path may contain wildcard characters like `[` or `]`.
- Use `Resolve-Path` before destructive filesystem work.
- Do not blindly escape like Bash.
- Use `--%` only when proper quoting still fails, only for native Windows commands, and only as a last resort. It is Windows-only.

## 7. JSON and web/API work

- Build objects first, then serialize them. Do not hand-concatenate JSON strings when objects will do.
- Use `ConvertTo-Json -Depth N` deliberately. The default depth is too shallow for many real payloads.
- Use `ConvertFrom-Json` for JSON input.
- Prefer `Get-Content -Raw | ConvertFrom-Json` for file-backed JSON.
- Prefer `Invoke-RestMethod` for JSON APIs so responses become objects immediately.
- Be explicit about encoding when writing files for other tools. Default to UTF-8 for modern workflows when interoperability matters.

## 8. Safety rules

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
- Do not hide errors with `-ErrorAction SilentlyContinue` unless there is a specific, justified reason.

## 9. Script quality

- In automation scripts, prefer:

```powershell
Set-StrictMode -Version Latest
$ErrorActionPreference = 'Stop'
```

- Use `try/catch` when failure should stop or be reported clearly.
- Use approved verbs for function names.
- Use `param()` blocks with typed parameters.
- For destructive functions, use `[CmdletBinding(SupportsShouldProcess)]`.
- Return objects from functions, not display formatting.
- Keep shell-specific assumptions explicit in comments or help text.

## 10. AI mistake checklist

Before giving a PowerShell answer, check for these failures:

- Bash syntax pasted into PowerShell
- `cmd.exe`, PowerShell 5.1, and PowerShell 7 confused together
- Linux path assumptions on Windows
- Text parsing where object properties exist
- Aliases used in durable scripts
- `curl` treated as guaranteed real curl
- JSON serialized without enough `-Depth`
- Dangerous delete/move commands without safety checks
- Native command success checked loosely instead of with `$LASTEXITCODE`
- Outdated Windows PowerShell-only assumptions applied to PowerShell 7

## 11. Output style for future agents

- State the target shell first.
- Prefer commands that can be copied safely as written.
- For multi-step workflows, explain each step briefly.
- For risky commands, provide a dry run first.
- If translating from Bash docs, say that the command was translated for PowerShell.
- If compatibility matters, provide separate `pwsh` and `powershell.exe` variants instead of pretending one command fits both.
