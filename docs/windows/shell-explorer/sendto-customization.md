---
title: Send To Customization
category: windows
tags: [windows, shell, sendto, context-menu]
last_updated: 2026-02-23
---

# Send To Customization

The **Send To** context menu entry (right-click → Send to) is backed by a real folder you can edit freely. Adding or removing shortcuts in that folder immediately changes what appears in the menu — no registry editing required.

## Opening the Send To Folder

The fastest way is via the **Run dialog** (`Win+R`):

```
shell:SendTo
```

This opens `%APPDATA%\Microsoft\Windows\SendTo` in Explorer.

You can also navigate there directly in Explorer or PowerShell:

```powershell
# Navigate to the folder
explorer $env:APPDATA\Microsoft\Windows\SendTo

# List current entries
Get-ChildItem "$env:APPDATA\Microsoft\Windows\SendTo"
```

## Adding a Send To Destination

Drop any **shortcut** (`.lnk` file) or **folder shortcut** into the Send To folder:

### Example: Add a folder as a Send To destination

1. Open `shell:SendTo`
2. Right-click inside → **New → Shortcut**
3. Set the target to the destination folder path, e.g. `C:\Work\Inbox`
4. Give it a friendly name like `Work Inbox`

Or automate it with PowerShell:

```powershell
$sendTo   = [System.Environment]::GetFolderPath('SendTo')
$shell    = New-Object -ComObject WScript.Shell
$lnk      = $shell.CreateShortcut("$sendTo\Work Inbox.lnk")
$lnk.TargetPath = "C:\Work\Inbox"
$lnk.Save()
```

### Example: Add a Notepad shortcut

```powershell
$sendTo   = [System.Environment]::GetFolderPath('SendTo')
$shell    = New-Object -ComObject WScript.Shell
$lnk      = $shell.CreateShortcut("$sendTo\Notepad.lnk")
$lnk.TargetPath = "$env:SystemRoot\notepad.exe"
$lnk.Save()
```

When you right-click a file and choose **Send to → Notepad**, Windows opens the file in Notepad.

## Removing a Send To Entry

Delete the corresponding shortcut from the Send To folder:

```powershell
$sendTo = [System.Environment]::GetFolderPath('SendTo')
Remove-Item "$sendTo\Compressed (zipped) folder.ZFSendToTarget" -ErrorAction SilentlyContinue
```

Or simply open `shell:SendTo` in Explorer and delete the file manually.

## Default Send To Entries

A fresh Windows install includes several built-in entries:

| Entry | What it does |
|-------|-------------|
| Compressed (zipped) folder | Zips the selected file(s) into a new archive |
| Desktop (create shortcut) | Creates a desktop shortcut to the item |
| Documents | Copies the file to the Documents folder |
| Fax recipient | Opens the fax wizard (legacy) |
| Mail recipient | Attaches the file to a new email message |
| Bluetooth device | Sends via Bluetooth (if adapter present) |

## Notes

- There is **no all-users Send To folder** in modern Windows. Each user profile has its own at `%APPDATA%\Microsoft\Windows\SendTo`.
- Changes take effect immediately — no need to restart Explorer.
- The target of a Send To shortcut can be an application (the file will be passed as an argument), a folder (the file will be copied there), or a network location.
- Shortcuts to batch scripts or PowerShell scripts work well as Send To targets for quick-processing workflows.

## Related

- [Shell & Explorer](README.md)
- [Context Menu Tweaks](context-menu-tweaks.md)
- [Shell Namespace Shortcuts](shell-namespace-shortcuts.md)
- [Windows Hub](../README.md)
