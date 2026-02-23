---
title: How to Set and Modify Environment Variables
category: windows
tags: [windows, environment-variables, how-to]
last_updated: 2026-02-23
---

# How to Set and Modify Environment Variables

Several methods exist for reading and writing environment variables on Windows.

## Notes

- **System Properties GUI** – `sysdm.cpl` → Advanced → Environment Variables
- **`setx` (CMD)** – persists a variable for the current user: `setx MY_VAR "value"`
  - Use `/M` flag to write to the system scope (requires elevation)
  - `setx` does **not** affect the current CMD session
- **PowerShell** – `[System.Environment]::SetEnvironmentVariable('MY_VAR','value','User')` (or `'Machine'`)
- **Registry** – write directly to `HKCU\Environment` or `HKLM\...\Environment` (see [User vs. System Scope](user-vs-system-scope.md))
- After setting a variable, broadcast `WM_SETTINGCHANGE` or restart the shell to pick up the change

## Related

- [Environment Variables Hub](README.md)
- [User vs. System Scope](user-vs-system-scope.md)
- [Windows Hub](../README.md)
