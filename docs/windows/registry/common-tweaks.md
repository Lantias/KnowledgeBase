---
title: Common Registry Tweaks
category: windows
tags: [windows, registry, tweaks]
last_updated: 2026-02-23
---

# Common Registry Tweaks

Frequently applied registry modifications for Windows customization.

## Notes

- **Disable Startup Delay** – `HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Serialize` → `StartupDelayInMSec` = `0` (DWORD)
- **Show file extensions** – `HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Advanced` → `HideFileExt` = `0`
- **Show hidden files** – same key as above → `Hidden` = `1`
- **Restore classic context menu (Win 11)** – `HKCU\SOFTWARE\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InprocServer32` → default value = empty string
- **Disable action center** – `HKCU\SOFTWARE\Policies\Microsoft\Windows\Explorer` → `DisableNotificationCenter` = `1`
- Always back up the registry (`File → Export` in regedit) before making changes
- Use `.reg` files for repeatable tweaks; document each change with a comment

## Related

- [Registry Hub](README.md)
- [Key Locations](key-locations.md)
- [Context Menu Tweaks](../shell-explorer/context-menu-tweaks.md)
- [Windows Hub](../README.md)
