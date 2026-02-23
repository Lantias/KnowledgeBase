---
title: Shell Namespace Shortcuts
category: windows
tags: [windows, shell, namespace]
last_updated: 2026-02-23
---

# Shell Namespace Shortcuts

Windows exposes special locations through `shell:` URIs and CLSID-based shortcuts. These allow quick navigation to system folders without needing to know the real path.

## Notes

- `shell:AppsFolder` – opens the hidden Applications virtual folder
- `shell:SendTo` – opens the current user's Send To folder
- `shell:Startup` – opens the current user's Startup folder
- `shell:Common Startup` – opens the all-users Startup folder
- CLSIDs can be entered as `shell:::{CLSID}` in the Run dialog or address bar
- Many CLSIDs are documented in the registry under `HKCR\CLSID`

## Related

- [Shell & Explorer](README.md)
- [Virtual Folders & AppsFolder](virtual-folders-appsfolder.md)
- [Windows Hub](../README.md)
