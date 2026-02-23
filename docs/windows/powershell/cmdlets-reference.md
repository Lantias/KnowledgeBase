---
title: Cmdlets Reference
category: windows
tags: [windows, powershell, cmdlets, reference]
last_updated: 2026-02-23
---

# Cmdlets Reference

A reference for commonly used PowerShell cmdlets grouped by task.

## Notes

- **File system**: `Get-ChildItem`, `Copy-Item`, `Move-Item`, `Remove-Item`, `Get-Content`, `Set-Content`
- **Processes**: `Get-Process`, `Stop-Process`, `Start-Process`
- **Services**: `Get-Service`, `Start-Service`, `Stop-Service`, `Restart-Service`
- **Networking**: `Test-NetConnection`, `Resolve-DnsName`, `Get-NetAdapter`
- **Aliases**: Many Unix-style aliases exist (`ls`, `cat`, `cp`, `mv`) – use `Get-Alias` to list them
- **Help**: `Get-Help <cmdlet> -Full` shows full documentation; `Update-Help` refreshes local help files
- **Pipeline**: PowerShell passes objects, not text – `Get-Process | Where-Object CPU -gt 10 | Sort-Object CPU -Descending`

## Related

- [PowerShell Hub](README.md)
- [Scripting Patterns](scripting-patterns.md)
- [Windows Hub](../README.md)
