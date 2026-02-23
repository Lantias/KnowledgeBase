---
title: Shell Namespace Shortcuts
category: windows
tags: [windows, shell, namespace]
last_updated: 2026-02-23
---

# Shell Namespace Shortcuts

Windows exposes special locations through `shell:` URIs and CLSID-based shortcuts. These allow quick navigation to system folders without needing to know the real path on disk.

## What Are Shell Namespace Shortcuts?

The Windows Shell Namespace is a virtual hierarchy that sits on top of the filesystem. It includes real folders, virtual folders (like the Control Panel or Network), and system locations that have no single filesystem path. `shell:` URIs are human-readable shortcuts into this namespace.

You can enter them anywhere Windows accepts a path:
- **Run dialog** (`Win+R`): type `shell:SendTo` and press Enter
- **Explorer address bar**: type `shell:AppsFolder` and press Enter
- **`Start-Process` in PowerShell**: `Start-Process "shell:Startup"`
- **`explorer.exe` from CMD**: `explorer shell:Common Startup`

## Common `shell:` URIs

| URI | Opens |
|-----|-------|
| `shell:AppsFolder` | Hidden Applications virtual folder (all installed apps) |
| `shell:SendTo` | Current user's Send To folder |
| `shell:Startup` | Current user's Startup folder (`%APPDATA%\Microsoft\Windows\Start Menu\Programs\Startup`) |
| `shell:Common Startup` | All-users Startup folder (requires elevation to write) |
| `shell:Desktop` | Current user's Desktop folder |
| `shell:Downloads` | Current user's Downloads folder |
| `shell:Personal` | Current user's Documents folder |
| `shell:My Music` | Current user's Music folder |
| `shell:My Pictures` | Current user's Pictures folder |
| `shell:My Video` | Current user's Videos folder |
| `shell:Recent` | Recent items (Jump Lists source) |
| `shell:Fonts` | Windows Fonts folder (`C:\Windows\Fonts`) |
| `shell:Programs` | Current user's Start Menu Programs folder |
| `shell:Common Programs` | All-users Start Menu Programs folder |
| `shell:Quick Launch` | Quick Launch toolbar folder (legacy) |
| `shell:Temp` | Current user's temp folder (`%TEMP%`) |
| `shell:Windows` | Windows directory (`C:\Windows`) |
| `shell:System` | System32 directory |
| `shell:ControlPanelFolder` | Control Panel virtual folder |
| `shell:NetworkPlacesFolder` | Network virtual folder |

## CLSID-Based Shortcuts

Every virtual folder and many system locations are registered with a **CLSID** (Class Identifier) — a GUID in curly braces. You can navigate directly to them using the `shell:::` syntax:

```
shell:::{CLSID}
```

For example, to open the Applications folder by CLSID:

```
shell:::{4234D49B-0245-4DF3-B780-3893943456E5}
```

### Finding CLSIDs

CLSIDs for known folders are registered under:

```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\FolderDescriptions
HKEY_CLASSES_ROOT\CLSID
```

You can query all registered Shell folders from PowerShell:

```powershell
Get-ChildItem 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\FolderDescriptions' |
    ForEach-Object {
        [PSCustomObject]@{
            Name  = (Get-ItemProperty $_.PSPath).Name
            CLSID = $_.PSChildName
        }
    } | Sort-Object Name
```

### Notable CLSIDs

| CLSID | Location |
|-------|----------|
| `{4234D49B-0245-4DF3-B780-3893943456E5}` | AppsFolder (Applications) |
| `{21EC2020-3AEA-1069-A2DD-08002B30309D}` | Control Panel (classic view) |
| `{26EE0668-A00A-44D7-9371-BEB064C98683}` | Control Panel (category view) |
| `{F02C1A0D-BE21-4350-88B0-7367FC96EF3C}` | Network |
| `{031E4825-7B94-4DC3-B131-E946B44C8DD5}` | Libraries |
| `{7B81BE6A-CE2B-4676-A29E-EB907A5126C5}` | Programs and Features |
| `{BB64F8A7-BEE7-4E1A-AB8D-7D8273F7FDB6}` | Action Center |

## Creating Desktop Shortcuts to Shell Locations

To make a shortcut that opens a `shell:` location, create a `.lnk` file with the target set to `explorer.exe` and the argument set to the `shell:` URI:

```powershell
$shell = New-Object -ComObject WScript.Shell
$shortcut = $shell.CreateShortcut("$env:USERPROFILE\Desktop\Startup Folder.lnk")
$shortcut.TargetPath = "explorer.exe"
$shortcut.Arguments  = "shell:Startup"
$shortcut.Save()
```

Or via CMD:
```batch
explorer shell:Startup
```

## Related

- [Shell & Explorer](README.md)
- [Virtual Folders & AppsFolder](virtual-folders-appsfolder.md)
- [Send To Customization](sendto-customization.md)
- [Windows Hub](../README.md)
