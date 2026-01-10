Below is a **concise, well-structured Obsidian note**, summarized and suitable for technical study or revision.

---

## UAC Bypass – GUI-Based Auto-Elevation (Summary)

### Overview

GUI-based UAC bypass techniques demonstrate how certain **auto-elevated Windows binaries** can be abused to obtain a **High Integrity Level (High IL)** command prompt **without triggering a UAC prompt**.  
These methods are **educational** and generally **not practical in real-world attacks**, as they require access to a graphical session.

---

### Environment Setup

- Target: Windows VM (used for all tasks)
    
- Access method: RDP or browser-based view
    

```bash
xfreerdp /v:10.65.130.247 /u:attacker /p:Password321
```

---

## Key Concept: Auto-Elevation

- Some trusted Windows binaries are configured to **auto-elevate**.
    
- They run with **High IL** even when launched by a standard user.
    
- No UAC consent dialog is required.
    
- Any process spawned from them **inherits the same elevated token**.
    

---

## Case Study 1: `msconfig`

### Why It Works

- `msconfig.exe` auto-elevates by design.
    
- Runs as **High IL** without showing a UAC prompt.
    

### Bypass Technique

1. Launch **msconfig**.
    
2. Verify High IL using **Process Hacker**.
    
3. Navigate to **Tools** tab.
    
4. Launch **Command Prompt** from the built-in tools menu.
    
5. The spawned `cmd.exe` inherits **High IL**.
    

### Result

- High integrity command prompt obtained without UAC interaction.
    

### Flag Retrieval

```cmd
C:\flags\GetFlag-msconfig.exe
```

---

## Case Study 2: `azman.msc`

### Why It Works

- `.msc` files are executed via **mmc.exe**.
    
- `mmc.exe` auto-elevates when launching certain consoles.
    
- `azman.msc` runs with **High IL**.
    

### Bypass Technique

1. Run `azman.msc`.
    
2. Confirm High IL via **Process Hacker**.
    
3. Open **Help** inside the application.
    
4. Right-click → **View Source** (spawns `notepad.exe`).
    
5. In Notepad:
    
    - File → Open
        
    - Set file type to **All Files**
        
    - Navigate to `C:\Windows\System32`
        
    - Right-click `cmd.exe` → Open
        

### Result

- `cmd.exe` launches with **High IL**, inheriting the elevated token from `mmc.exe`.
    

### Flag Retrieval

```cmd
C:\flags\GetFlag-azman.exe
```

---

## Key Takeaways

- Auto-elevated binaries can bypass UAC without exploits.
    
- Child processes inherit the parent’s integrity level.
    
- GUI-based bypasses rely on **legitimate application features**.
    
- Useful for understanding **Windows privilege escalation mechanics**, not stealth attacks.
    

---

