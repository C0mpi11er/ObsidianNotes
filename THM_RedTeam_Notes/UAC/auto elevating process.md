
Below is a **clean, concise Obsidian note**, summarized for **quick revision and clear understanding**, written in a style suitable for technical notes.

---

## AutoElevate – UAC Bypass Summary

### What Is AutoElevate?

**AutoElevate** is a Windows feature that allows certain **trusted executables** to run with **High Integrity Level (High IL)** automatically, **without triggering a UAC prompt**.  
This is common for Control Panel components and system configuration tools.

---

## Core AutoElevation Requirements

For an executable to auto-elevate:

### Mandatory Conditions

- Must be **signed by Microsoft (Windows Publisher)**
    
- Must reside in a **trusted directory**, such as:
    
    - `%SystemRoot%\System32\`
        
    - `%ProgramFiles%\`
        

---

## Additional AutoElevation Mechanisms

Depending on application type:

### 1. Executable Manifest (`.exe`)

- Must declare:
    
    ```xml
    <autoElevate>true</autoElevate>
    ```
    
- Example: `msconfig.exe`
    
- Can be verified using **sigcheck**:
    
    ```cmd
    sigcheck64.exe -m C:\Windows\System32\msconfig.exe
    ```
    

---

### 2. MMC Snap-ins (`.msc`)

- `.msc` files are launched via `mmc.exe`
    
- Auto-elevation depends on the snap-in
    
- Most built-in Windows `.msc` files auto-elevate
    

---

### 3. Windows Allowlist

- Some executables auto-elevate even **without a manifest**
    
- Examples:
    
    - `pkgmgr.exe`
        
    - `spinstall.exe`
        

---

### 4. COM Auto-Elevation

- Certain COM objects can request elevation
    
- Controlled via registry configuration (COM Elevation Moniker)
    

---

## Case Study: `fodhelper.exe`

### Why `fodhelper.exe` Is Important

- Manages Windows optional features
    
- Auto-elevates by default
    
- **Does not require a GUI**
    
- Can be abused from a **remote medium integrity shell**
    

Used in real-world malware (e.g., **Glupteba**).

---

## Registry & ProgID Fundamentals

### How Windows Chooses Which Program to Run

Windows checks **ProgIDs** in the registry to determine how to open files or protocols.

Key lookup location:

```
HKEY_CLASSES_ROOT
```

This is a **merged view** of:

|Path|Purpose|
|---|---|
|HKLM\Software\Classes|System-wide associations|
|HKCU\Software\Classes|User-specific overrides|

**HKCU always takes priority over HKLM**.

---

## Why `ms-settings` Matters

- `fodhelper.exe` opens resources using the **`ms-settings` ProgID**
    
- By creating a **user-level override** in:
    
    ```
    HKCU\Software\Classes\ms-settings
    ```
    
- The attacker controls what command is executed
    

Since `fodhelper.exe` auto-elevates:

> Any spawned process inherits a **High IL token**, bypassing UAC

---

## Exploitation Flow (High-Level)

1. Attacker has:
    
    - Admin group membership
        
    - Medium Integrity shell
        
2. Create HKCU override for `ms-settings`
    
3. Set command to a reverse shell
    
4. Add empty `DelegateExecute` value (required)
    
5. Execute `fodhelper.exe`
    
6. Reverse shell spawns with **High Integrity**
    
7. UAC bypassed successfully
    

---

## Integrity Verification

- Before:
    
    ```
    Medium Mandatory Level
    ```
    
- After:
    
    ```
    High Mandatory Level
    ```
    

This confirms a **true UAC bypass**, not just admin membership.

---

## Cleanup (Anti-Forensics)

Registry artifacts persist and must be removed:

```cmd
reg delete HKCU\Software\Classes\ms-settings\ /f
```

Failure to clean up:

- Breaks system behavior
    
- Leaves detection indicators
    
- Interferes with future tasks
    

---

## Key Takeaways

- UAC is a **policy boundary**, not a hard security boundary
    
- AutoElevate exists for usability, not security
    
- Registry precedence enables user-level hijacking
    
- Token inheritance enables privilege escalation
    
- `fodhelper.exe` is dangerous because it:
    
    - Auto-elevates
        
    - Uses registry lookups
        
    - Works without GUI access
        

---

