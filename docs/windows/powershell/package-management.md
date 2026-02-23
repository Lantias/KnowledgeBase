---
title: Package Management
category: windows
tags: [windows, powershell, package-management, winget, chocolatey]
last_updated: 2026-02-23
---

# Package Management

Windows now has several package managers to choose from. Each occupies a different niche — pick the right tool for the job.

## Overview

| Tool | Built-in? | Admin Required? | Best For |
|------|-----------|-----------------|---------|
| **WinGet** | ✅ Yes (Win 10 1809+) | For some packages | GUI apps, Microsoft Store apps |
| **PowerShellGet / PSGallery** | ✅ Yes | No | PowerShell modules |
| **Chocolatey** | ❌ Install separately | Usually yes | CLI tools, dev tools, enterprise |
| **Scoop** | ❌ Install separately | No | CLI tools, portable installs |

## WinGet

**WinGet** is Microsoft's official package manager, included with Windows 10 (version 1809+) and Windows 11. It sources packages from the Microsoft Store and the [WinGet Community repository](https://github.com/microsoft/winget-pkgs).

```powershell
# Search for a package
winget search vscode

# Install a package by ID
winget install Microsoft.VisualStudioCode

# Install silently
winget install --id Git.Git --silent

# List installed packages
winget list

# Upgrade a specific package
winget upgrade Microsoft.VisualStudioCode

# Upgrade all upgradeable packages
winget upgrade --all

# Uninstall a package
winget uninstall Microsoft.VisualStudioCode

# Show package details
winget show Git.Git
```

### Finding Package IDs

Package IDs are case-sensitive and follow the format `Publisher.ApplicationName`:

```powershell
# Search and show IDs
winget search notepad++ --exact
```

Or browse the community repository at [winget.run](https://winget.run).

### Exporting and Restoring Packages

```powershell
# Export installed packages to a JSON file
winget export -o packages.json

# Restore from exported file (on a new machine)
winget import -i packages.json --ignore-unavailable
```

## PowerShellGet / PSGallery

**PowerShellGet** is the built-in module for installing PowerShell modules, scripts, and DSC resources from the [PowerShell Gallery](https://www.powershellgallery.com/).

```powershell
# Search for a module
Find-Module PSReadLine

# Install a module (current user, no elevation needed)
Install-Module -Name Pester -Scope CurrentUser

# Install for all users (requires elevation)
Install-Module -Name Az -Scope AllUsers

# List installed modules
Get-InstalledModule

# Update a module
Update-Module -Name Pester

# Uninstall a module
Uninstall-Module -Name Pester

# Install a script from PSGallery
Install-Script -Name Get-WindowsAutoPilotInfo
```

### NuGet Provider

The first time you use `Install-Module` you may be prompted to install the NuGet provider. Accept the prompt or pre-install it:

```powershell
Install-PackageProvider -Name NuGet -Force
```

## Chocolatey

**Chocolatey** is the most established community package manager for Windows. It has a large package catalog and supports enterprise features (package approval, private feeds, etc.).

### Installing Chocolatey

Always check [chocolatey.org/install](https://chocolatey.org/install) for the current installation script, and review its contents before running:

```powershell
# Install Chocolatey (run as Administrator)
Set-ExecutionPolicy Bypass -Scope Process -Force
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

> ⚠️ Always verify the contents of any remote install script before running it with elevated privileges.

### Using Chocolatey

```powershell
# Search for packages
choco search nodejs

# Install a package
choco install nodejs -y

# Install multiple packages
choco install git vscode 7zip -y

# List installed packages
choco list --local-only

# Upgrade a package
choco upgrade nodejs -y

# Upgrade all packages
choco upgrade all -y

# Uninstall a package
choco uninstall nodejs -y
```

## Scoop

**Scoop** installs programs to `%USERPROFILE%\scoop` without requiring administrator rights. It is designed for CLI tools and developer utilities.

### Installing Scoop

```powershell
# Install Scoop (run as a normal user)
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
irm get.scoop.sh | iex
```

### Using Scoop

```powershell
# Search for a package
scoop search ripgrep

# Install a package
scoop install ripgrep

# Add the 'extras' bucket for more packages
scoop bucket add extras

# Install from extras bucket
scoop install windows-terminal

# List installed packages
scoop list

# Update all packages
scoop update *

# Uninstall a package
scoop uninstall ripgrep
```

## Choosing the Right Tool

- **GUI / end-user apps** → WinGet (e.g., `winget install 7zip.7zip`)
- **PowerShell modules** → PSGallery (`Install-Module`)
- **Dev tools with enterprise needs** → Chocolatey
- **CLI tools without admin rights** → Scoop
- **Multiple machines / reproducible setups** → `winget export` / `winget import`

## Related

- [PowerShell Hub](README.md)
- [Cmdlets Reference](cmdlets-reference.md)
- [Scripting Patterns](scripting-patterns.md)
- [Windows Hub](../README.md)
