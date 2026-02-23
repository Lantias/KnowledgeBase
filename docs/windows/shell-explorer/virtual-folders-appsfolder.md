---
title: Virtual Folders & AppsFolder
category: windows
tags: [windows, shell, virtual-folders, appsfolder]
last_updated: 2026-02-23
---

# Virtual Folders & AppsFolder

Windows exposes several virtual folders that have no real filesystem path. The Applications folder (`AppsFolder`) lists all installed apps including Store apps.

## Notes

- Access via `shell:AppsFolder` or `explorer shell:::{4234D49B-0245-4DF3-B780-3893943456E5}`
- Apps inside `AppsFolder` can be pinned to the taskbar or Start
- Virtual folders cannot be browsed with standard filesystem tools
- `IKnownFolder` API is the programmatic way to resolve virtual folder paths
- Some virtual folders are user-specific; others are system-wide

## Related

- [Shell & Explorer](README.md)
- [Shell Namespace Shortcuts](shell-namespace-shortcuts.md)
- [Windows Hub](../README.md)
