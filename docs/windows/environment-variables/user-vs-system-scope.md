---
title: User vs. System Scope
category: windows
tags: [windows, environment-variables, scope]
last_updated: 2026-02-23
---

# User vs. System Scope

Windows environment variables exist at two levels that are merged at login time.

## Notes

- **System (machine) variables** – apply to all users; stored in `HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Environment`
- **User variables** – apply only to the logged-in user; stored in `HKCU\Environment`
- When both levels define the same variable, the user-level value takes precedence (except `PATH`, which is concatenated)
- `PATH` is special: the effective path is `System PATH` + `User PATH`
- Changes to system variables require administrator rights
- Changes take effect for new processes; existing shells must be restarted or refreshed

## Related

- [Environment Variables Hub](README.md)
- [How to Set and Modify](how-to-set-and-modify.md)
- [Windows Hub](../README.md)
