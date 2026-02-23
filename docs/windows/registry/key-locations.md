---
title: Registry Key Locations
category: windows
tags: [windows, registry, reference]
last_updated: 2026-02-23
---

# Registry Key Locations

A map of the Windows Registry hives, their purposes, and the most important paths within each.

## Registry Structure Overview

The Windows Registry is a hierarchical database of configuration settings. It is organised into top-level containers called **hives**. Each hive contains **keys** (like folders), **subkeys**, and **values** (the actual data items).

```
HIVE\Key\Subkey
    ValueName    REG_SZ    "data"
```

### Value Types

| Type | Name | Used for |
|------|------|---------|
| `REG_SZ` | String | Plain text strings |
| `REG_EXPAND_SZ` | Expandable string | Strings with `%variable%` references |
| `REG_DWORD` | 32-bit integer | Numeric flags and settings |
| `REG_QWORD` | 64-bit integer | Large numeric values |
| `REG_BINARY` | Binary | Raw binary data |
| `REG_MULTI_SZ` | Multi-string | Lists of strings |

## The Five Root Hives

### HKEY_LOCAL_MACHINE (HKLM)

Machine-wide settings that apply to all users. **Requires administrator rights to write.**

| Path | Contents |
|------|---------|
| `SOFTWARE\Microsoft\Windows\CurrentVersion` | Core Windows paths, startup entries, shell settings |
| `SOFTWARE\Microsoft\Windows NT\CurrentVersion` | OS version, build number, registered owner |
| `SYSTEM\CurrentControlSet\Control\Session Manager\Environment` | System-wide environment variables |
| `SYSTEM\CurrentControlSet\Services` | Service configuration (start type, executable path, etc.) |
| `SOFTWARE\Microsoft\Windows\CurrentVersion\Run` | Programs that launch at boot for all users |
| `SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall` | Installed programs (64-bit) |
| `SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall` | Installed programs (32-bit on 64-bit Windows) |
| `SOFTWARE\Classes` | File associations, COM classes, shell extensions (system part) |
| `SYSTEM\CurrentControlSet\Control\ComputerName\ComputerName` | Machine name |

```powershell
# Read OS version
Get-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion' |
    Select-Object ProductName, CurrentBuild, UBR
```

### HKEY_CURRENT_USER (HKCU)

Settings for the currently logged-in user. **Writable without elevation.**

| Path | Contents |
|------|---------|
| `Environment` | User environment variables |
| `SOFTWARE\Microsoft\Windows\CurrentVersion\Run` | User-level startup programs |
| `SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced` | Explorer settings (show hidden files, extensions, etc.) |
| `SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders` | Redirected shell folder paths |
| `SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\RecentDocs` | Recent documents list |
| `SOFTWARE\Microsoft\Windows\CurrentVersion\Themes` | Current desktop theme |
| `SOFTWARE\Classes` | Per-user file associations (overrides HKLM) |
| `Console` | Command Prompt window settings |
| `Keyboard Layout\Preload` | Installed keyboard layouts |

```powershell
# List user startup entries
Get-ItemProperty 'HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run'
```

### HKEY_CLASSES_ROOT (HKCR)

A **merged, read-only view** of:
- `HKLM\SOFTWARE\Classes` (system-wide)
- `HKCU\SOFTWARE\Classes` (per-user, takes precedence)

Contains file type associations, ProgIDs, COM class registrations, and shell extension registrations. Writing to `HKCR` writes to `HKLM\SOFTWARE\Classes` — to make per-user changes, write to `HKCU\SOFTWARE\Classes` directly.

| Path | Contents |
|------|---------|
| `HKCR\<.ext>` | File extension registration (e.g., `.txt`) |
| `HKCR\<ProgID>` | Application/document type descriptor |
| `HKCR\<ProgID>\shell\<verb>\command` | Context menu action |
| `HKCR\CLSID\{GUID}` | COM class registrations |
| `HKCR\*\shell` | Context menu verbs for all files |
| `HKCR\Directory\shell` | Context menu verbs for folders |
| `HKCR\Directory\Background\shell` | Context menu verbs for folder backgrounds |

### HKEY_USERS (HKU)

Contains a subkey for every loaded user profile, keyed by the user's **Security Identifier (SID)**:

```
HKEY_USERS\S-1-5-21-<domain>-<machine>-<user>-<RID>
```

Special SIDs:
- `.DEFAULT` — template used for new profiles
- `S-1-5-18` — SYSTEM account
- `S-1-5-19` — Local Service
- `S-1-5-20` — Network Service

`HKCU` is an alias that automatically points to the current user's SID subkey.

### HKEY_CURRENT_CONFIG (HKCC)

A live view of the current hardware profile. Rarely modified directly. Aliases `HKLM\SYSTEM\CurrentControlSet\Hardware Profiles\Current`.

## Navigating the Registry

### regedit.exe (GUI)

The built-in graphical registry editor. Use the address bar to jump directly to a path:

```
Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion
```

Press `Ctrl+F` to search for keys, values, or data.

**Always export (`File → Export`) a key before modifying it** so you can restore it if something goes wrong.

### reg.exe (Command Line)

```batch
:: Query a value
reg query "HKCU\Environment" /v PATH

:: Query all values under a key
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion"

:: Add / update a value
reg add "HKCU\Environment" /v MY_VAR /t REG_SZ /d "my value" /f

:: Delete a value
reg delete "HKCU\Environment" /v MY_VAR /f

:: Export a key to a .reg file
reg export "HKCU\SOFTWARE\MyApp" C:\Backup\myapp.reg

:: Import a .reg file
reg import C:\Backup\myapp.reg
```

### PowerShell Registry Provider

PowerShell exposes the registry as a drive (`HKLM:`, `HKCU:`):

```powershell
# List values under a key
Get-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion'

# Read a single value
(Get-ItemProperty 'HKCU:\Environment').PATH

# Set a value
Set-ItemProperty 'HKCU:\Environment' -Name 'MY_VAR' -Value 'hello' -Type String

# Create a new key
New-Item 'HKCU:\SOFTWARE\MyApp' -Force

# Delete a key and all subkeys
Remove-Item 'HKCU:\SOFTWARE\MyApp' -Recurse -Force

# Check if a key exists
Test-Path 'HKCU:\SOFTWARE\MyApp'
```

## Related

- [Registry Hub](README.md)
- [Common Tweaks](common-tweaks.md)
- [User vs. System Scope (Env Vars)](../environment-variables/user-vs-system-scope.md)
- [Context Menu Tweaks](../shell-explorer/context-menu-tweaks.md)
- [Windows Hub](../README.md)
