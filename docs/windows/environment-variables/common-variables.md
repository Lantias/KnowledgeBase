---
title: Common Environment Variables
category: windows
tags: [windows, environment-variables, reference]
last_updated: 2026-02-23
---

# Common Environment Variables

A quick reference for the built-in Windows environment variables you encounter most often.

## What Are Environment Variables?

Environment variables are key-value pairs stored in the process environment. Every Windows process inherits a copy from its parent. They provide a portable, configuration-independent way to reference paths, usernames, and system settings without hard-coding them.

You can reference a variable anywhere by wrapping its name in `%` signs (CMD/batch) or prefixing with `$env:` (PowerShell):

```batch
:: CMD / batch
echo %USERPROFILE%
cd "%TEMP%"
```

```powershell
# PowerShell
Write-Host $env:USERPROFILE
Set-Location $env:TEMP
```

## Reference Table

| Variable | Typical Value | Description |
|----------|--------------|-------------|
| `%USERPROFILE%` | `C:\Users\Alice` | Current user's home directory |
| `%APPDATA%` | `C:\Users\Alice\AppData\Roaming` | Roaming application data (synced with roaming profiles) |
| `%LOCALAPPDATA%` | `C:\Users\Alice\AppData\Local` | Local application data (not synced) |
| `%TEMP%` / `%TMP%` | `C:\Users\Alice\AppData\Local\Temp` | Per-user temporary files directory |
| `%SystemRoot%` | `C:\Windows` | Windows installation directory |
| `%SystemDrive%` | `C:` | Drive letter of the Windows installation |
| `%ProgramFiles%` | `C:\Program Files` | 64-bit program files directory |
| `%ProgramFiles(x86)%` | `C:\Program Files (x86)` | 32-bit program files on 64-bit Windows |
| `%ProgramData%` | `C:\ProgramData` | All-users application data (not in user profiles) |
| `%PUBLIC%` | `C:\Users\Public` | Shared folder visible to all local users |
| `%PATH%` | (semicolon-separated list) | Ordered list of directories searched for executables |
| `%PATHEXT%` | `.COM;.EXE;.BAT;...` | Extensions that are treated as executables |
| `%COMPUTERNAME%` | `DESKTOP-ABC123` | Machine's NetBIOS name |
| `%USERNAME%` | `Alice` | Currently logged-on user name |
| `%USERDOMAIN%` | `CONTOSO` | Domain (or local machine name for local accounts) |
| `%USERDOMAIN_ROAMINGPROFILE%` | `CONTOSO` | Domain used by the roaming profile |
| `%OS%` | `Windows_NT` | Always `Windows_NT` on modern Windows |
| `%PROCESSOR_ARCHITECTURE%` | `AMD64` | CPU architecture (`AMD64`, `x86`, `ARM64`) |
| `%NUMBER_OF_PROCESSORS%` | `8` | Logical processor count |
| `%WINDIR%` | `C:\Windows` | Alias for `%SystemRoot%` |
| `%HOMEDRIVE%` | `C:` | Drive letter of the user profile |
| `%HOMEPATH%` | `\Users\Alice` | Path portion of the user profile (without drive) |
| `%COMSPEC%` | `C:\Windows\System32\cmd.exe` | Path to the default command interpreter |

## AppData Sub-folders

`%APPDATA%` and `%LOCALAPPDATA%` are the standard locations for per-user application data. The distinction matters for portability:

| Variable | Roams with Profile? | Use for |
|----------|---------------------|---------|
| `%APPDATA%` | Yes (with roaming profiles) | Settings that should follow the user across machines |
| `%LOCALAPPDATA%` | No | Caches, large data, machine-specific configs |
| `%ProgramData%` | N/A (all-users) | Machine-wide application data accessible by all users |

## The PATH Variable

`PATH` is a semicolon-delimited list of directories. When you type a command without a full path, Windows searches each directory in order until it finds an executable matching the name:

```
C:\Windows\System32;C:\Windows;C:\Program Files\Git\cmd;...
```

The effective `PATH` is the **system PATH concatenated with the user PATH** (user appended after system). See [User vs. System Scope](user-vs-system-scope.md) for details.

## Viewing All Variables

**CMD:**
```batch
set
```

**PowerShell:**
```powershell
Get-ChildItem Env:
# Or filter by name
$env:PATH -split ';'
```

## Related

- [Environment Variables Hub](README.md)
- [User vs. System Scope](user-vs-system-scope.md)
- [How to Set and Modify](how-to-set-and-modify.md)
- [Windows Hub](../README.md)
- [Shell Namespace Shortcuts](../shell-explorer/shell-namespace-shortcuts.md) – `shell:` URIs (similar syntax, different system)

