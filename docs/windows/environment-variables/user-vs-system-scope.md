---
title: User vs. System Scope
category: windows
tags: [windows, environment-variables, scope]
last_updated: 2026-02-23
---

# User vs. System Scope

Windows environment variables exist at two levels — **user** and **system (machine)** — that are merged together when a new process starts. Understanding the difference is important for knowing who sees a change and where to look when a variable has an unexpected value.

## The Two Scopes

### System (Machine) Variables

System variables apply to **all users** on the machine. They are stored in the registry at:

```
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment
```

- Require **administrator rights** to modify
- Changes affect every new login session
- Managed via Group Policy in domain environments

### User Variables

User variables apply only to the **currently logged-in user**. They are stored in the registry at:

```
HKEY_CURRENT_USER\Environment
```

- Writable by the user without elevation
- Override system variables of the same name (with the exception of `PATH`)
- Stored per-profile, so each user account has its own set

## How Variables Are Merged

When a new interactive session starts (login, new terminal, new process), Windows builds the effective environment by:

1. Loading all **system** variables
2. Loading all **user** variables, overwriting any system variable with the same name

The one important exception is **`PATH`**:

> `PATH` is **concatenated**, not overridden. The effective PATH is:
> `System PATH` + `;` + `User PATH`

This means you can extend the path by adding entries to either scope without losing the other's entries. You can verify this from PowerShell:

```powershell
# Show all PATH directories in order
$env:PATH -split ';'
```

## Precedence Summary

| Scenario | Winner |
|----------|--------|
| System variable `FOO=A`, User variable `FOO=B` | User wins: `FOO=B` |
| System `PATH=X;Y`, User `PATH=Z` | Concatenated: `X;Y;Z` |
| Variable set only in System scope | All users see it |
| Variable set only in User scope | Only that user sees it |

## Process-Level Variables

A process can also set or modify its own environment variables at runtime. These changes:
- Are **not persisted** to the registry
- Are visible only within that process and any child processes it spawns
- Disappear when the process exits

In PowerShell:
```powershell
# Set a variable for the current session only
$env:MY_TEMP_VAR = "hello"

# This does NOT persist after the PowerShell window closes
```

To make a change permanent you must write to the registry (see [How to Set and Modify](how-to-set-and-modify.md)).

## When Changes Take Effect

Modifying the registry values directly does **not** update running processes. The operating system broadcasts a `WM_SETTINGCHANGE` message to notify running applications that the environment has changed, but most applications (including most CMD and PowerShell sessions) do not react to it automatically.

**Practical rule:** open a new terminal window after changing any environment variable to pick up the change.

If you need to refresh without opening a new window, you can read directly from the registry:

```powershell
# Reload PATH from both scopes in the current PowerShell session
$machinePath = [System.Environment]::GetEnvironmentVariable('PATH', 'Machine')
$userPath    = [System.Environment]::GetEnvironmentVariable('PATH', 'User')
$env:PATH    = "$machinePath;$userPath"
```

## Related

- [Environment Variables Hub](README.md)
- [Common Variables](common-variables.md)
- [How to Set and Modify](how-to-set-and-modify.md)
- [Registry Key Locations](../registry/key-locations.md)
- [Windows Hub](../README.md)
