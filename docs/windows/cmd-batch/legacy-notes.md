---
title: Legacy Notes (CMD & Batch)
category: windows
tags: [windows, cmd, batch, legacy]
last_updated: 2026-02-23
---

# Legacy Notes (CMD & Batch)

Quirks, gotchas, and compatibility considerations for CMD and batch scripting. CMD/batch is a legacy environment with many rough edges — prefer PowerShell for new work, but these notes help when you must maintain or read existing scripts.

## When to Use CMD/Batch

- Maintaining existing `.bat` or `.cmd` files
- Environments where PowerShell is unavailable or restricted
- Simple wrapper scripts that launch other executables
- Pre-boot or recovery contexts where PowerShell is not yet loaded

For anything more complex, use PowerShell.

## Script Basics

### Suppressing Echo

By default, CMD echoes every command before executing it. Put `@echo off` at the top to silence this:

```batch
@echo off
:: The @ suppresses echo for the echo off line itself
:: Without @, you'd see "echo off" printed once
```

To suppress just one line, prefix it with `@`:

```batch
@echo off
echo This line is printed
@echo This line is also printed but the command itself isn't re-echoed
```

### Comments

Both `REM` and `::` work as comments:

```batch
REM This is a comment
:: This is also a comment (faster, but avoid inside FOR loops)
```

`::` is slightly faster because CMD doesn't parse it as a command, but it can cause issues inside `for` loops or `if` blocks where CMD tries to parse it as a label. Use `REM` inside loops.

## Variables

### Positional Parameters

Batch files receive command-line arguments as `%1` through `%9`:

```batch
@echo off
echo First argument:  %1
echo Second argument: %2
```

`%0` is the script's own path/name.

### The `%~dp0` Idiom

`%~dp0` is one of the most useful batch idioms — it expands to the **drive and path** of the currently executing script, always ending with a backslash:

```batch
@echo off
:: Always refer to files relative to the script location
set CONFIG=%~dp0config.ini
set LOGDIR=%~dp0logs\
```

| Modifier | Meaning |
|----------|---------|
| `%~f0` | Full path of the script |
| `%~d0` | Drive letter only (`C:`) |
| `%~p0` | Path only (no drive, no filename) |
| `%~n0` | Filename without extension |
| `%~x0` | Extension only (`.bat`) |
| `%~dp0` | Drive + path (most common) |
| `%~nx0` | Filename + extension |

### SET and Variable Expansion

Variables are set with `SET` and expanded with `%`:

```batch
set MY_VAR=Hello World
echo %MY_VAR%
```

> ⚠️ **No spaces around `=`**: `set MY_VAR = value` creates a variable named `MY_VAR ` (with a trailing space) and assigns ` value` (with a leading space).

### Delayed Expansion

CMD expands variables **at parse time**, not at execution time. Inside a `for` loop or `if` block, `%var%` is evaluated once when the block is parsed, not on each iteration:

```batch
@echo off
setlocal enabledelayedexpansion

set COUNT=0
for %%i in (a b c) do (
    set /a COUNT+=1
    echo Item !COUNT!: %%i   :: Use !COUNT!, not %COUNT%
)
```

Rules:
- Add `setlocal enabledelayedexpansion` at the top of the section that needs it
- Use `!variable!` instead of `%variable%` inside loops and blocks
- `setlocal`/`endlocal` create a variable scope — changes revert at `endlocal`

## ERRORLEVEL

`ERRORLEVEL` holds the exit code of the last command. Testing it requires special syntax:

```batch
:: "if errorlevel N" is true if the exit code is >= N
some-command.exe
if errorlevel 1 (
    echo Command failed with error code %errorlevel%
    exit /b 1
)

:: For exact comparison, use == (but watch for cmd quirks)
if %errorlevel% == 0 echo Success
```

Do **not** use `if errorlevel 0` to check for success — it is always true (0 ≥ 0). Check `if errorlevel 1` for failure.

## Quoting Paths

Always quote paths that may contain spaces:

```batch
:: Bad — fails if C:\Program Files has a space
cd C:\Program Files\App

:: Good
cd "C:\Program Files\App"

:: When referencing variables, quote the expansion
set MYDIR=C:\My Documents
cd "%MYDIR%"
```

## Useful Built-in Commands

| Command | Description |
|---------|-------------|
| `echo` | Print text; `echo.` prints a blank line |
| `set /a` | Arithmetic: `set /a RESULT=5+3` |
| `set /p` | Prompt for input: `set /p NAME=Enter name: ` |
| `call` | Call another batch file and return |
| `goto :label` | Jump to a label in the file |
| `exit /b N` | Exit the current script (not the CMD window) with code N |
| `pushd` / `popd` | Save and restore current directory |
| `for /f` | Parse command output or file lines |
| `findstr` | Simple text search (like grep) |
| `xcopy` / `robocopy` | File copy with more options than `copy` |
| `timeout` | Pause execution: `timeout /t 5 /nobreak` |

## Calling PowerShell from Batch

When you need PowerShell capabilities from a batch file:

```batch
@echo off
:: Run a PowerShell command inline
powershell -NoProfile -Command "Write-Host 'Hello from PowerShell'"

:: Run a PowerShell script
powershell -NoProfile -ExecutionPolicy Bypass -File "%~dp0script.ps1"
```

## Related

- [CMD & Batch Hub](README.md)
- [PowerShell](../powershell/README.md)
- [Windows Hub](../README.md)
