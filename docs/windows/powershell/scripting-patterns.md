---
title: Scripting Patterns
category: windows
tags: [windows, powershell, scripting, patterns]
last_updated: 2026-02-23
---

# Scripting Patterns

Reusable PowerShell patterns for common scripting tasks. These patterns cover error handling, logging, parameter validation, and script organization.

## Execution Policy

Before running scripts locally, you need to set an appropriate execution policy. The recommended setting for developers is:

```powershell
# Allow locally created scripts and remote scripts with a valid signature
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
```

| Policy | What it allows |
|--------|---------------|
| `Restricted` | No scripts at all (default on fresh Windows) |
| `AllSigned` | Only scripts signed by a trusted publisher |
| `RemoteSigned` | Local scripts run freely; downloaded scripts need a signature |
| `Unrestricted` | All scripts run (not recommended for security) |
| `Bypass` | Nothing is blocked (use only for CI/automated contexts) |

Use `-Scope CurrentUser` to avoid needing admin rights.

## Error Handling

### Making All Errors Terminating

By default, many PowerShell errors are *non-terminating* — the script continues running. Set `$ErrorActionPreference` to `Stop` at the top of scripts where you want any error to abort execution:

```powershell
$ErrorActionPreference = 'Stop'
```

### try / catch / finally

```powershell
try {
    $content = Get-Content -Path 'C:\data\config.json' -ErrorAction Stop
    $config  = $content | ConvertFrom-Json
}
catch [System.IO.FileNotFoundException] {
    Write-Error "Config file not found: $_"
    exit 1
}
catch {
    Write-Error "Unexpected error: $_"
    throw   # re-throw if you want the caller to see it
}
finally {
    # Runs whether or not an exception occurred — good for cleanup
    Write-Verbose "Config load attempt completed"
}
```

## Parameter Validation with CmdletBinding

Making scripts behave like proper cmdlets improves usability and safety:

```powershell
[CmdletBinding(SupportsShouldProcess)]
param (
    [Parameter(Mandatory, HelpMessage = 'Path to the target directory')]
    [ValidateScript({ Test-Path $_ -PathType Container })]
    [string]$Path,

    [Parameter()]
    [ValidateRange(1, 100)]
    [int]$MaxItems = 10,

    [Parameter()]
    [switch]$Force
)
```

- `[CmdletBinding()]` adds common parameters like `-Verbose`, `-Debug`, `-WhatIf`, `-ErrorAction`
- `[Parameter(Mandatory)]` prompts the user if the parameter is not supplied
- `[ValidateScript({ })]` runs any expression to validate the input
- `[ValidateRange()]`, `[ValidateSet()]`, `[ValidateLength()]` provide type-specific validation
- `SupportsShouldProcess` enables `-WhatIf` and `-Confirm` support

## Logging

### Write-Verbose for Optional Output

```powershell
[CmdletBinding()]
param()

Write-Verbose "Starting process — use -Verbose to see this"
Write-Host "This always appears"
```

Callers can toggle verbose output with the `-Verbose` switch without modifying the script.

### Log to a Dated File

```powershell
function Write-Log {
    param([string]$Message, [string]$Level = 'INFO')
    $timestamp = Get-Date -Format 'yyyy-MM-dd HH:mm:ss'
    $line      = "[$timestamp] [$Level] $Message"
    Add-Content -Path $script:LogFile -Value $line
    if ($Level -eq 'ERROR') { Write-Error $Message }
    else { Write-Verbose $line }
}

$script:LogFile = "C:\Logs\myscript_$(Get-Date -Format 'yyyyMMdd').log"
Write-Log "Script started"
```

## Idempotency

Scripts should be safe to run multiple times. Always check before creating or modifying:

```powershell
# Create a directory only if it doesn't exist
if (-not (Test-Path $targetPath)) {
    New-Item -ItemType Directory -Path $targetPath | Out-Null
}

# Add to PATH only if not already present
$currentPath = [System.Environment]::GetEnvironmentVariable('PATH', 'User')
if ($currentPath -notlike "*$newDir*") {
    [System.Environment]::SetEnvironmentVariable('PATH', "$currentPath;$newDir", 'User')
}

# Install module only if missing
if (-not (Get-Module -ListAvailable -Name Pester)) {
    Install-Module -Name Pester -Scope CurrentUser -Force
}
```

## Dot-Sourcing vs. Modules

### Dot-Sourcing

Loads a script's contents into the current session. Quick and simple for sharing functions:

```powershell
# Load functions from a helper script
. "$PSScriptRoot\helpers.ps1"
```

`$PSScriptRoot` is the directory of the currently running script — always use it instead of relative paths to ensure the script works from any working directory.

### PowerShell Modules (.psm1)

For reusable libraries, create a proper module. A minimal module is just a `.psm1` file:

```powershell
# helpers.psm1
function Get-Greeting {
    param([string]$Name)
    "Hello, $Name!"
}

Export-ModuleMember -Function Get-Greeting
```

Place the module in a directory under `$env:PSModulePath` (e.g., `%USERPROFILE%\Documents\PowerShell\Modules\Helpers\`) and import it:

```powershell
Import-Module Helpers
Get-Greeting -Name "World"
```

## $PSScriptRoot and Relative Paths

Always use `$PSScriptRoot` for paths relative to the script file. This ensures the script works regardless of the working directory:

```powershell
# Good — always finds config.json next to the script
$config = Join-Path $PSScriptRoot 'config.json'

# Bad — depends on current working directory
$config = '.\config.json'
```

## WhatIf Support

For scripts that make changes, support `-WhatIf` so users can preview actions:

```powershell
[CmdletBinding(SupportsShouldProcess)]
param([string]$Path)

if ($PSCmdlet.ShouldProcess($Path, "Delete directory")) {
    Remove-Item -Path $Path -Recurse -Force
}
```

Running the script with `-WhatIf` will print what would happen without actually doing it.

## Related

- [PowerShell Hub](README.md)
- [Cmdlets Reference](cmdlets-reference.md)
- [Environment Variables](../environment-variables/README.md)
- [Windows Hub](../README.md)
