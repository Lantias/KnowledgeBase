---
title: Legacy Notes (CMD & Batch)
category: windows
tags: [windows, cmd, batch, legacy]
last_updated: 2026-02-23
---

# Legacy Notes (CMD & Batch)

Quirks, gotchas, and compatibility considerations for CMD and batch scripting.

## Notes

- Batch files use `%1`–`%9` for positional arguments; `%~dp0` expands to the script's directory
- Variable expansion is done at parse time, not runtime – use `setlocal enabledelayedexpansion` and `!var!` for runtime expansion inside loops
- `ERRORLEVEL` is checked with `if errorlevel 1 ...` (not `==`); any value ≥ 1 matches
- `REM` and `::` both work as comments; `::` is faster but can cause issues inside `for` loops
- `@echo off` at the top suppresses command echo; `@` before a line suppresses just that line
- Avoid spaces in paths where possible; always quote paths that may contain spaces: `"%~dp0script.bat"`
- Prefer PowerShell for new scripts; use CMD/batch only for legacy compatibility or minimal-environment scenarios

## Related

- [CMD & Batch Hub](README.md)
- [PowerShell](../powershell/README.md)
- [Windows Hub](../README.md)
