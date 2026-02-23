---
title: Common Registry Tweaks
category: windows
tags: [windows, registry, tweaks]
last_updated: 2026-02-23
---

# Common Registry Tweaks

Frequently applied registry modifications for Windows customization. All tweaks here use per-user (`HKCU`) keys unless noted, so they don't require administrator rights.

> ⚠️ **Always back up the registry before making changes**: open `regedit.exe` → File → Export, select the key you are about to change, and save the `.reg` file. To restore, double-click the backup file.

## Explorer Settings

### Show File Extensions

By default Windows hides known file extensions. This tweak reveals them:

```powershell
Set-ItemProperty 'HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced' `
    -Name HideFileExt -Value 0 -Type DWord
# Restart Explorer to apply immediately
Stop-Process -Name explorer -Force
```

`.reg` equivalent:
```reg
Windows Registry Editor Version 5.00

[HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced]
"HideFileExt"=dword:00000000
```

### Show Hidden Files and Folders

```powershell
Set-ItemProperty 'HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced' `
    -Name Hidden -Value 1 -Type DWord
Stop-Process -Name explorer -Force
```

### Show Protected Operating System Files

```powershell
Set-ItemProperty 'HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced' `
    -Name ShowSuperHidden -Value 1 -Type DWord
Stop-Process -Name explorer -Force
```

### Disable Startup Delay

Windows adds a delay before launching startup programs for a smoother login experience. Disable it for faster startup program loading:

```powershell
$key = 'HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Serialize'
if (-not (Test-Path $key)) { New-Item $key -Force | Out-Null }
Set-ItemProperty $key -Name StartupDelayInMSec -Value 0 -Type DWord
```

## Windows 11: Restore Classic Context Menu

Windows 11 hides most context menu entries behind "Show more options". This tweak restores the classic full context menu as the default:

```powershell
$path = 'HKCU:\SOFTWARE\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InprocServer32'
New-Item -Path $path -Force | Out-Null
Set-ItemProperty -Path $path -Name '(Default)' -Value '' -Type String
# Restart Explorer to apply
Stop-Process -Name explorer -Force
```

To revert and restore the Windows 11 menu:
```powershell
Remove-Item 'HKCU:\SOFTWARE\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}' `
    -Recurse -Force
Stop-Process -Name explorer -Force
```

## Startup Programs

### View Startup Entries

```powershell
# User-level startup (current user)
Get-ItemProperty 'HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run'

# System-level startup (all users, requires elevation)
Get-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run'
```

### Add a User-Level Startup Entry

```powershell
Set-ItemProperty 'HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run' `
    -Name 'MyTool' -Value '"C:\Tools\mytool.exe" --background'
```

### Remove a Startup Entry

```powershell
Remove-ItemProperty 'HKCU:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run' -Name 'MyTool'
```

## Notification Center

### Disable Action Center (Notification Center)

```powershell
$key = 'HKCU:\SOFTWARE\Policies\Microsoft\Windows\Explorer'
if (-not (Test-Path $key)) { New-Item $key -Force | Out-Null }
Set-ItemProperty $key -Name DisableNotificationCenter -Value 1 -Type DWord
```

## Accessibility & UX

### Disable Window Animation (Faster UI)

```powershell
Set-ItemProperty 'HKCU:\Control Panel\Desktop\WindowMetrics' `
    -Name MinAnimate -Value '0' -Type String
```

### Disable Lock Screen

```powershell
# Requires HKLM, needs elevation
$key = 'HKLM:\SOFTWARE\Policies\Microsoft\Windows\Personalization'
if (-not (Test-Path $key)) { New-Item $key -Force | Out-Null }
Set-ItemProperty $key -Name NoLockScreen -Value 1 -Type DWord
```

## Applying Tweaks with .reg Files

`.reg` files are the standard portable format for registry changes. They can be double-clicked to merge, or applied silently:

```batch
:: Silent import (no prompts)
reg import tweak.reg /reg:64
```

### .reg File Syntax

```reg
Windows Registry Editor Version 5.00

; This is a comment

[HKEY_CURRENT_USER\SOFTWARE\Example]
"StringValue"="hello"
"DWordValue"=dword:00000001
"ExpandString"=hex(2):25,00,54,00,45,00,4d,00,50,00,25,00,00,00

; Delete a value by setting it to -
"DeleteMe"=-

; Delete an entire key with a leading -
[-HKEY_CURRENT_USER\SOFTWARE\Example\DeleteThisKey]
```

## Related

- [Registry Hub](README.md)
- [Key Locations](key-locations.md)
- [Context Menu Tweaks](../shell-explorer/context-menu-tweaks.md)
- [Shell & Explorer](../shell-explorer/README.md)
- [Windows Hub](../README.md)
