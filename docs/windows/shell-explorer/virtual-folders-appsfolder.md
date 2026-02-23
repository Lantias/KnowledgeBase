---
title: Virtual Folders & AppsFolder
category: windows
tags: [windows, shell, virtual-folders, appsfolder]
last_updated: 2026-02-23
---

# Virtual Folders & AppsFolder

Windows exposes several **virtual folders** — locations that appear in Explorer but have no single physical path on disk. The **Applications folder** (`AppsFolder`) is the most useful of these, listing every installed application including modern Store (UWP) apps that are otherwise hard to find.

## What Are Virtual Folders?

A virtual folder is a Shell Namespace object backed by a COM object rather than a directory on disk. Examples include:

- **This PC** – aggregates drives and devices
- **Network** – browses network locations
- **Control Panel** – lists system settings panels
- **Recycle Bin** – backed by `$Recycle.Bin` folders on each drive
- **Libraries** – aggregates folders from multiple locations
- **AppsFolder** – lists all installed applications

Because they have no real filesystem path, you cannot browse them with standard tools like `dir` or `Get-ChildItem` on a path. You must use Shell APIs or the `shell:` URI scheme.

## AppsFolder

`AppsFolder` is a virtual folder that aggregates:
- Classic Win32 installed applications (those with Start Menu entries)
- Modern UWP/Store applications
- Progressive Web Apps (PWAs) pinned by a browser

### Opening AppsFolder

**Run dialog (`Win+R`) or Explorer address bar:**
```
shell:AppsFolder
```

**By CLSID:**
```
shell:::{4234D49B-0245-4DF3-B780-3893943456E5}
```

**From PowerShell:**
```powershell
Start-Process "explorer.exe" -ArgumentList "shell:AppsFolder"
```

**From CMD:**
```batch
explorer shell:AppsFolder
```

### Pinning Apps from AppsFolder

Apps visible in `AppsFolder` can be pinned to the Taskbar or Start Menu even when they don't appear through normal means. Right-click any app in the folder for the pin options. This is especially useful for UWP apps or tools installed outside the standard `%ProgramFiles%` path.

### Listing AppsFolder Contents Programmatically

You can enumerate `AppsFolder` using PowerShell with the Shell COM object:

```powershell
$shell   = New-Object -ComObject Shell.Application
$folder  = $shell.NameSpace("shell:AppsFolder")
$items   = $folder.Items()

foreach ($item in $items) {
    [PSCustomObject]@{
        Name = $item.Name
        Path = $item.Path   # may be an AppUserModelID, not a filesystem path
    }
}
```

The `Path` property for UWP apps returns the **Application User Model ID (AUMID)** (e.g., `Microsoft.WindowsCalculator_8wekyb3d8bbwe!App`), not a filesystem path.

### Launching UWP Apps by AUMID

Once you have an app's AUMID you can launch it directly:

```powershell
Start-Process "shell:AppsFolder\Microsoft.WindowsCalculator_8wekyb3d8bbwe!App"
```

Or via the `explorer` command:
```batch
explorer shell:AppsFolder\Microsoft.WindowsCalculator_8wekyb3d8bbwe!App
```

## IKnownFolder API

For programmatic access to virtual and known folders from C++/.NET code, Windows provides the **`IKnownFolder`** interface (part of the Shell API, `ShlObj.h`). The managed equivalent in .NET is `Environment.GetFolderPath()` using the `Environment.SpecialFolder` enum.

```csharp
// .NET: resolve the current user's Desktop path
string desktop = Environment.GetFolderPath(Environment.SpecialFolder.Desktop);
```

From PowerShell:
```powershell
[System.Environment]::GetFolderPath('Desktop')
[System.Environment]::GetFolderPath('ApplicationData')   # %APPDATA%
[System.Environment]::GetFolderPath('LocalApplicationData') # %LOCALAPPDATA%
[System.Environment]::GetFolderPath('CommonApplicationData')
[System.Environment]::GetFolderPath('ProgramFiles')
[System.Environment]::GetFolderPath('Windows')
```

`IKnownFolder` is preferred over hard-coding paths because folder locations can be redirected (e.g., roaming profiles, folder redirection in enterprise environments).

## User-Specific vs. System-Wide Virtual Folders

| Folder | Scope | Shell URI |
|--------|-------|-----------|
| Desktop | Per-user | `shell:Desktop` |
| Startup | Per-user | `shell:Startup` |
| Programs (Start Menu) | Per-user | `shell:Programs` |
| Common Startup | All-users | `shell:Common Startup` |
| Common Programs | All-users | `shell:Common Programs` |
| AppsFolder | All installed apps | `shell:AppsFolder` |
| Fonts | System | `shell:Fonts` |
| ControlPanelFolder | System | `shell:ControlPanelFolder` |

All-users folders typically require administrator rights to modify.

## Related

- [Shell & Explorer](README.md)
- [Shell Namespace Shortcuts](shell-namespace-shortcuts.md)
- [Windows Hub](../README.md)
