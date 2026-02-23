---
title: Context Menu Tweaks
category: windows
tags: [windows, shell, context-menu, registry]
last_updated: 2026-02-23
---

# Context Menu Tweaks

Right-click context menus can be extended or trimmed via the registry or third-party tools.

## Notes

- File-type entries live under `HKCR\<ext>\shell\` and `HKCR\<ProgID>\shell\`
- Background (desktop/folder) entries live under `HKCR\Directory\Background\shell\`
- The `Extended` DWORD value hides an entry unless Shift is held
- A `LegacyDisable` named value (empty string) under the verb key suppresses a shell-extension context menu entry
- Windows 11 moved most entries to the "Show more options" sub-menu; classic menu can be restored via registry
- Tools like ShellExView help manage shell extension DLLs

## Related

- [Shell & Explorer](README.md)
- [Send To Customization](sendto-customization.md)
- [Registry](../registry/README.md)
- [Windows Hub](../README.md)
