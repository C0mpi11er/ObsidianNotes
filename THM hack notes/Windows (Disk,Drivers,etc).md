Here are concise yet informative notes on the **`driverquery`**, **`chkdsk`**, and **`sfc /scannow`** commands in Windows:

---

### **1. `driverquery`**  
**Purpose**: Lists all installed **device drivers** (signed/unsigned, versions, and vendors).  
**Usage**:  
```cmd
driverquery
```
**Common Options**:  
- `driverquery /v` → **Verbose** mode (shows detailed driver info).  
- `driverquery /fo csv` → Exports data in **CSV format** (for scripting).  
- `driverquery /si` → Displays **signed driver** info.  

**Use Case**:  
- Check for **outdated/corrupt drivers**.  
- Identify **unsigned drivers** (potential security risks).  

---

### **2. `chkdsk` (Check Disk)**  
**Purpose**: Scans and repairs **disk errors** (bad sectors, file system corruption).  
**Usage**:  
```cmd
chkdsk C: /f /r
```
**Common Options**:  
- `/f` → **Fixes errors** (requires disk dismount/restart if C: is in use).  
- `/r` → Locates **bad sectors** and recovers data (implies `/f`).  
- `/x` → Forces dismount of the drive (if needed).  

**Use Case**:  
- Fix **corrupted filesystems** (e.g., after a crash).  
- Diagnose **failing hard drives**.  

**Note**: Run as **Administrator**. Schedule a reboot if scanning the system drive (`C:`).  

---

### **3. `sfc /scannow` (System File Checker)**  
**Purpose**: Scans and restores **corrupted/missing Windows system files**.  
**Usage**:  
```cmd
sfc /scannow
```
**How It Works**:  
- Compares system files against a **cache** (`C:\Windows\System32\dllcache`).  
- Replaces damaged files with correct versions.  

**Common Follow-ups**:  
- If errors persist, run:  
  ```cmd
  DISM /Online /Cleanup-Image /RestoreHealth
  ```
  (Fixes the system image before retrying `sfc`).  

**Use Case**:  
- Fix **DLL errors**, crashes, or Windows instability.  
- Repair **missing/corrupt system files** (e.g., after malware).  

**Note**: Requires **Admin rights** and may take 15–30 minutes.  

---

### **Summary Table**  
| Command           | Purpose                          | Key Options                     | When to Use                          |
|-------------------|----------------------------------|---------------------------------|--------------------------------------|
| **`driverquery`** | List device drivers             | `/v`, `/si`, `/fo csv`         | Debug driver issues.                 |
| **`chkdsk`**     | Repair disk errors              | `/f`, `/r`, `/x`               | Fix filesystem corruption.           |
| **`sfc /scannow`**| Repair system files             | (None; runs a full scan)       | Fix Windows crashes/DLL errors.      |

---

### **Pro Tips**  
1. **Order of Troubleshooting**:  
   - Run `sfc /scannow` **before** `chkdsk` for software-related issues.  
   - Use `chkdsk` for **physical disk errors**.  
2. **Backup First**: Both `chkdsk` and `sfc` can make changes to your system.  
3. **Logs**:  
   - `sfc` logs results in `CBS.log` (`findstr /c:"[SR]" %windir%\Logs\CBS\CBS.log`).  
   - `chkdsk` logs to the **Event Viewer**.  

