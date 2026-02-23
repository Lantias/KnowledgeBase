---
title: Context Menu Tweaks
category: windows
tags: [windows, shell, context-menu, registry]
last_updated: 2026-02-23
---

# Context Menu Tweaks

The Windows right-click context menu is driven by the **Shell Namespace** registry entries. You can add new entries, hide existing ones, or remove clutter using the registry or third-party tools — no code compilation required.

## Where Context Menu Entries Are Stored

Context menu verbs live under several registry locations depending on what you right-click:

| Right-click target | Registry path |
|--------------------|---------------|
| A file with a specific extension | `HKCR\<ext>\shell\` and `HKCR\<ProgID>\shell\` |
| All files | `HKCR\*\shell\` |
| Background of a folder or Desktop | `HKCR\Directory\Background\shell\` |
| A folder itself | `HKCR\Directory\shell\` |
| All folders | `HKCR\Folder\shell\` |
| Desktop background | `HKCR\DesktopBackground\shell\` |

`HKCR` is a merged view of `HKLM\SOFTWARE\Classes` and `HKCU\SOFTWARE\Classes`. Writing to `HKCU\SOFTWARE\Classes` applies changes per-user without administrator rights.

## Adding a Custom Context Menu Entry

A verb key has this structure:

```
HKCR\<target>\shell\<VerbName>
    (Default)     = "Menu Label"
    Icon          = "path_to_icon.ico"

HKCR\<target>\shell\<VerbName>\command
    (Default)     = "\"C:\path\to\app.exe\" \"%1\""
```

`%1` is replaced by Windows with the path of the right-clicked file.

### Example: Open any file in Notepad

```powershell
# Add "Open with Notepad" to all files (per-user, no elevation needed)
$base = 'HKCU:\SOFTWARE\Classes\*\shell\OpenWithNotepad'
New-Item -Path "$base\command" -Force | Out-Null
Set-ItemProperty -Path $base           -Name '(Default)' -Value 'Open with Notepad'
Set-ItemProperty -Path $base           -Name 'Icon'      -Value 'notepad.exe,0'
Set-ItemProperty -Path "$base\command" -Name '(Default)' -Value '"notepad.exe" "%1"'
```

### Example: Open a folder background in Windows Terminal

```powershell
$base = 'HKCU:\SOFTWARE\Classes\Directory\Background\shell\wt'
New-Item -Path "$base\command" -Force | Out-Null
Set-ItemProperty -Path $base           -Name '(Default)' -Value 'Open in Windows Terminal'
Set-ItemProperty -Path $base           -Name 'Icon'      -Value 'wt.exe'
Set-ItemProperty -Path "$base\command" -Name '(Default)' -Value 'wt.exe -d "%V"'
```

`%V` (for background/folder verbs) expands to the folder path instead of `%1`.

## Hiding Entries Without Deleting Them

### Extended flag

Add a `Extended` DWORD value (any non-zero data) to a verb key to hide it from the standard menu. It will only appear when the user holds **Shift** while right-clicking:

```powershell
Set-ItemProperty 'HKCU:\SOFTWARE\Classes\*\shell\OpenWithNotepad' `
    -Name 'Extended' -Value 0 -Type DWord
```

To show it again, delete the `Extended` value.

### LegacyDisable

For shell extension DLLs (those registered under `HKCR\*\shellex\ContextMenuHandlers\`), add an empty `LegacyDisable` string value under the handler key to suppress it:

```
HKCR\*\shellex\ContextMenuHandlers\<HandlerName>
    LegacyDisable   = ""
```

This disables the extension without unregistering the DLL.

## Windows 11: Classic vs. Modern Context Menu

Windows 11 moved most third-party entries to the **"Show more options"** sub-menu (opened by pressing **Shift+F10** or clicking "Show more options"). To restore the classic full menu as default, apply this registry tweak:

```powershell
# Restore classic context menu in Windows 11 (per-user)
$path = 'HKCU:\SOFTWARE\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InprocServer32'
New-Item -Path $path -Force | Out-Null
Set-ItemProperty -Path $path -Name '(Default)' -Value '' -Type String
```

To revert (re-enable the modern menu):
```powershell
Remove-Item 'HKCU:\SOFTWARE\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}' -Recurse -Force
```

## Managing Shell Extension DLLs with ShellExView

[**ShellExView**](https://www.nirsoft.net/utils/shexview.html) (by NirSoft) is a free utility that lists all registered shell extensions and lets you enable/disable them with a checkbox. It is the easiest way to identify which DLL is responsible for a slow or unwanted context menu entry.

- Disabled extensions are flagged in the registry with a `LegacyDisable` value automatically by ShellExView.
- Use the **"Mark extensions from non-Microsoft"** color filter to quickly spot third-party additions.

## `.reg` File Examples

### Add "Open Terminal Here" to folder background

```reg
Windows Registry Editor Version 5.00

[HKEY_CURRENT_USER\SOFTWARE\Classes\Directory\Background\shell\wt]
@="Open in Windows Terminal"
"Icon"="wt.exe"

[HKEY_CURRENT_USER\SOFTWARE\Classes\Directory\Background\shell\wt\command]
@="wt.exe -d \"%V\""
```

### Remove a context menu entry

```reg
Windows Registry Editor Version 5.00

[-HKEY_CURRENT_USER\SOFTWARE\Classes\*\shell\OpenWithNotepad]
```

A leading `-` before the key path deletes the key when the `.reg` file is merged.

## Related

- [Shell & Explorer](README.md)
- [Send To Customization](sendto-customization.md)
- [Registry Key Locations](../registry/key-locations.md)
- [Common Registry Tweaks](../registry/common-tweaks.md)
- [Windows Hub](../README.md)
