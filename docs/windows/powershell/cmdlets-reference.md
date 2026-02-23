---
title: Cmdlets Reference
category: windows
tags: [windows, powershell, cmdlets, reference]
last_updated: 2026-02-23
---

# Cmdlets Reference

A reference for commonly used PowerShell cmdlets grouped by task. PowerShell cmdlets follow a consistent **Verb-Noun** naming convention (`Get-ChildItem`, `Remove-Item`, etc.), which makes them discoverable even without consulting docs.

## Getting Help

Before diving into individual cmdlets, remember that PowerShell's built-in help system is comprehensive:

```powershell
# Show full help for any cmdlet
Get-Help Get-ChildItem -Full

# Show examples only
Get-Help Get-ChildItem -Examples

# Update local help files (run as Administrator)
Update-Help

# List all cmdlets matching a pattern
Get-Command *-Service

# List all available aliases
Get-Alias
```

## File System

| Cmdlet | Alias(es) | Description |
|--------|-----------|-------------|
| `Get-ChildItem` | `ls`, `dir`, `gci` | List directory contents |
| `Get-Item` | `gi` | Get a specific file or directory object |
| `Copy-Item` | `cp`, `copy` | Copy files/directories |
| `Move-Item` | `mv`, `move` | Move/rename files/directories |
| `Remove-Item` | `rm`, `del`, `ri` | Delete files/directories |
| `New-Item` | `ni` | Create a new file, directory, or other item |
| `Rename-Item` | `rni` | Rename a file or directory |
| `Get-Content` | `cat`, `gc`, `type` | Read file contents |
| `Set-Content` | `sc` | Write content to a file (replaces) |
| `Add-Content` | `ac` | Append content to a file |
| `Test-Path` | | Check if a path exists |
| `Resolve-Path` | | Resolve a path to its absolute form |
| `Split-Path` | | Extract parts of a path |
| `Join-Path` | | Combine path segments |

### Examples

```powershell
# Recursively list all .log files modified in the last 7 days
Get-ChildItem -Path C:\Logs -Recurse -Filter *.log |
    Where-Object { $_.LastWriteTime -gt (Get-Date).AddDays(-7) }

# Copy a directory recursively
Copy-Item -Path C:\Source -Destination D:\Backup -Recurse

# Create a directory tree
New-Item -ItemType Directory -Path C:\Projects\MyApp\src -Force

# Append a timestamp line to a log file
Add-Content -Path C:\Logs\app.log -Value "[$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss')] Script started"

# Safely check before deleting
if (Test-Path $filePath) { Remove-Item $filePath }
```

## Processes

| Cmdlet | Description |
|--------|-------------|
| `Get-Process` | List running processes |
| `Stop-Process` | Kill a process by name or ID |
| `Start-Process` | Start a process |
| `Wait-Process` | Wait for a process to exit |

```powershell
# Find processes using more than 500 MB of RAM
Get-Process | Where-Object WorkingSet -gt 500MB | Sort-Object WorkingSet -Descending

# Kill all instances of Notepad
Stop-Process -Name notepad -Force

# Start an elevated PowerShell
Start-Process powershell -Verb RunAs

# Run a script and wait for it to finish
Start-Process python -ArgumentList "C:\scripts\run.py" -Wait
```

## Services

| Cmdlet | Description |
|--------|-------------|
| `Get-Service` | List services and their status |
| `Start-Service` | Start a stopped service |
| `Stop-Service` | Stop a running service |
| `Restart-Service` | Restart a service |
| `Set-Service` | Change service startup type or other properties |

```powershell
# List all running services
Get-Service | Where-Object Status -eq 'Running'

# Restart the Windows Update service (requires elevation)
Restart-Service wuauserv

# Change a service to start automatically
Set-Service -Name Spooler -StartupType Automatic
```

## Networking

| Cmdlet | Description |
|--------|-------------|
| `Test-NetConnection` | TCP connectivity and ping tests |
| `Resolve-DnsName` | DNS lookup |
| `Get-NetAdapter` | List network adapters |
| `Get-NetIPAddress` | List IP addresses |
| `Get-NetRoute` | Show routing table |

```powershell
# Test if port 443 is open on a host
Test-NetConnection -ComputerName github.com -Port 443

# DNS lookup
Resolve-DnsName github.com

# List adapters with their IP addresses
Get-NetIPAddress | Where-Object AddressFamily -eq 'IPv4' | Format-Table InterfaceAlias, IPAddress
```

## Objects, Pipeline, and Filtering

PowerShell passes **objects** through the pipeline, not plain text. This makes filtering, sorting, and transforming output much more reliable than text parsing.

```powershell
# The pipeline passes objects
Get-Process | Where-Object CPU -gt 10 | Sort-Object CPU -Descending | Select-Object -First 5

# Select only specific properties
Get-Service | Select-Object Name, Status, StartType

# Group processes by company
Get-Process | Group-Object Company | Sort-Object Count -Descending

# Export to CSV
Get-Process | Select-Object Name, Id, CPU |
    Export-Csv -Path C:\Temp\processes.csv -NoTypeInformation

# Convert to JSON
Get-Service | Select-Object Name, Status | ConvertTo-Json
```

### Useful Filtering Cmdlets

| Cmdlet | Description |
|--------|-------------|
| `Where-Object` | Filter objects by property condition |
| `Select-Object` | Select specific properties or first/last N items |
| `Sort-Object` | Sort by one or more properties |
| `Group-Object` | Group objects by a property |
| `Measure-Object` | Count, sum, average, min, max |
| `ForEach-Object` | Run a script block for each object |

## Output Formatting

```powershell
# Format as table (default for many cmdlets)
Get-Service | Format-Table -AutoSize

# Format as list (good for detail view)
Get-Service -Name wuauserv | Format-List *

# Output as wide (names only)
Get-Process | Format-Wide Name -Column 4

# Output to Out-GridView for interactive filtering (requires GUI)
Get-Process | Out-GridView -Title "Running Processes"
```

## Related

- [PowerShell Hub](README.md)
- [Scripting Patterns](scripting-patterns.md)
- [Package Management](package-management.md)
- [Windows Hub](../README.md)
