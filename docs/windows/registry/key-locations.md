---
title: Registry Key Locations
category: windows
tags: [windows, registry, reference]
last_updated: 2026-02-23
---

# Registry Key Locations

A map of important registry hives and paths.

## Notes

- **`HKEY_LOCAL_MACHINE` (HKLM)** – machine-wide settings; requires elevation to write
  - `SOFTWARE\Microsoft\Windows\CurrentVersion` – core Windows paths and settings
  - `SYSTEM\CurrentControlSet\Control\Session Manager\Environment` – system environment variables
  - `SOFTWARE\Microsoft\Windows NT\CurrentVersion` – OS version and build info
- **`HKEY_CURRENT_USER` (HKCU)** – current user's settings; writable without elevation
  - `Environment` – user environment variables
  - `SOFTWARE\Microsoft\Windows\CurrentVersion\Run` – user-level startup programs
- **`HKEY_CLASSES_ROOT` (HKCR)** – file associations and COM class registrations (merged view of `HKLM\SOFTWARE\Classes` and `HKCU\SOFTWARE\Classes`)
- **`HKEY_USERS`** – all loaded user hives; `HKEY_USERS\S-1-5-21-…` for individual accounts
- Use `regedit.exe` for GUI browsing; `reg query` / `reg add` for scripting

## Related

- [Registry Hub](README.md)
- [Common Tweaks](common-tweaks.md)
- [Windows Hub](../README.md)
