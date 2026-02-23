# PowerShell: Dot-Sourcing and The `$PROFILE`

When writing custom PowerShell functions (like a `Repair-WindowsApp` tool), you need a way to load them into your active session. This is done via **Dot-Sourcing** and can be made permanent using your **PowerShell `$PROFILE`**.

## 1. What is Dot-Sourcing?
By default, when you run a script, PowerShell opens it, runs the contents, and then "forgets" it. If the script contains a function, that function won't be available to use afterward. 

**Dot-sourcing** tells PowerShell to load the script's contents into your *current active memory*. 

**Syntax:**
```powershell
. .\apprep.ps1
```
* **The first dot (`.`)**: The command to load into memory.
* **The space (` `)**: Separates the command from the path.
* **The path (`.\apprep.ps1`)**: The path to the script (e.g., `.\` means current directory).

Once dot-sourced, any functions inside that script can be called from the command line like native Windows commands.

## 2. Making Scripts Permanent with `$PROFILE`
If you want your custom functions loaded automatically every time you open PowerShell, you must add the dot-source command to your `$PROFILE`. 

The `$PROFILE` is a special, hidden PowerShell script that automatically runs every time you launch a new PowerShell window.

### Step-by-Step: Setting up your `$PROFILE`

**1. Find your profile path:**
```powershell
$PROFILE
```

**2. Create the profile file (if it doesn't exist):**
Windows reserves the path for the profile but doesn't create the physical text file by default. Run this to create it safely:
```powershell
if (!(Test-Path $PROFILE)) { New-Item -Type File -Path $PROFILE -Force }
```

**3. Open the profile in a text editor:**
```powershell
notepad $PROFILE
# Or use VS Code: code $PROFILE
```

**4. Add your dot-source command:**
Add the dot-source command to the file. **Important:** Because the profile runs from anywhere, you must use the **absolute path** wrapped in quotes.
```powershell
# Loads custom Windows App Repair Tool on startup
. "C:\Exact\Path\To\Your\Script\apprep.ps1"
```
Save and close the file. The next time you open PowerShell, your tools will be instantly available.

## 3. Troubleshooting: Execution Policy Errors
Because you are now forcing PowerShell to run a script at startup, Windows Security might block it and throw a red "running scripts is disabled on this system" error.

To fix this, tell Windows to allow locally created scripts. Open PowerShell **as Administrator** and run:
```powershell
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
```
Press `Y` to confirm.