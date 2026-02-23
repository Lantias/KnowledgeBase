---
title: Package Management
category: windows
tags: [windows, powershell, package-management, winget, chocolatey]
last_updated: 2026-02-23
---

# Package Management

Tools for installing and managing software packages on Windows.

## Notes

- **WinGet** (built-in, Windows 10 1809+): `winget search <pkg>`, `winget install <id>`, `winget upgrade --all`
- **Chocolatey**: community-driven; see the [official install docs](https://chocolatey.org/install) – always review the install script contents before running any remote script with elevated privileges
- **PowerShellGet / PSGallery**: `Install-Module <name>`, `Find-Module <name>`, `Update-Module`
- **Scoop**: user-level installs with no admin requirement; `scoop install <app>`
- Prefer WinGet for GUI apps; PSGallery for PowerShell modules; Chocolatey or Scoop for CLI tools not on WinGet

## Related

- [PowerShell Hub](README.md)
- [Cmdlets Reference](cmdlets-reference.md)
- [Windows Hub](../README.md)
