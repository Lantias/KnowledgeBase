---
title: Scripting Patterns
category: windows
tags: [windows, powershell, scripting, patterns]
last_updated: 2026-02-23
---

# Scripting Patterns

Reusable PowerShell patterns for common scripting tasks.

## Notes

- **Error handling**: use `try/catch/finally`; set `$ErrorActionPreference = 'Stop'` to make non-terminating errors terminating
- **Parameter validation**: use `[CmdletBinding()]` and `[Parameter(Mandatory)]` to make scripts behave like proper cmdlets
- **Logging**: write to a dated log file with `Add-Content`; use `Write-Verbose` for optional output gated by `-Verbose`
- **Idempotency**: check for existence before creating (`if (-not (Test-Path $path)) { ... }`)
- **Dot-sourcing vs. modules**: dot-source (`. ./script.ps1`) for quick sharing of functions; use modules (`.psm1`) for reusable libraries
- **Execution policy**: `Set-ExecutionPolicy RemoteSigned -Scope CurrentUser` is the typical developer setting

## Related

- [PowerShell Hub](README.md)
- [Cmdlets Reference](cmdlets-reference.md)
- [Windows Hub](../README.md)
