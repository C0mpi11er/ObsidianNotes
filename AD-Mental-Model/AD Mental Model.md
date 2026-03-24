


Below is a **clear mental model + decision graph** you can internalize and reuse in real engagements.

---

# 🧠 ACTIVE DIRECTORY PENTESTING — MIND GRAPH

Think in **5 Phases (Looping System)**:

```
Recon → Enumeration → Foothold → Privilege Escalation → Lateral Movement → Domain Dominance
                         ↑______________________________________________|
```

Each phase feeds the next. You loop until **Domain Admin / Enterprise Admin**.

---

# 🔍 1. INITIAL ACCESS / RECON (AFTER NMAP)

You already ran:

```
nmap -sC -sV -p- <target>
```

### Now interpret, don’t scan blindly:

### If you see:

- `88 → Kerberos`
    
- `389 → LDAP`
    
- `445 → SMB`
    
- `53 → DNS`
    
- `5985 → WinRM`
    

👉 You are in **Active Directory environment**

---

## 🎯 NEXT MOVE: DOMAIN ENUMERATION (NO AUTH)

### Goal: Understand the domain structure

### Tools:

- `enum4linux`
    
- `crackmapexec`
    
- `ldapsearch`
    
- `smbclient`
    
- `rpcclient`
    

### Decision Tree:

```
[Anonymous SMB allowed?]
    ├── YES → enum4linux → users, shares, policies
    └── NO  → Try null/guest → move to creds hunting

[LDAP accessible?]
    ├── YES → ldapsearch → dump domain info
    └── NO  → skip

[Kerberos exposed?]
    ├── YES → AS-REP roasting check
```

---

# 🔐 2. CREDENTIAL ACCESS (FIRST BREAKTHROUGH)

This is where most beginners get stuck.

## 🎯 GOAL: Get ANY credential

---

## PATH A: AS-REP ROASTING (No creds needed)

```
GetNPUsers.py <domain>/ -no-pass
```

👉 If users have **"Do not require preauth"**

→ Crack hash → get password

---

## PATH B: KERBEROASTING (Need ANY low user)

If you somehow get:

- guest
    
- weak creds
    
- sprayed creds
    

Then:

```
GetUserSPNs.py
```

→ Crack service account hashes

---

## PATH C: PASSWORD SPRAY

```
crackmapexec smb <targets> -u users.txt -p 'Password123'
```

---

## PATH D: SMB / SHARE ENUM

```
smbclient -L //<target>
```

Look for:

- config files
    
- passwords
    
- scripts
    

---

## 🔁 DECISION NODE

```
[Got credentials?]
    ├── YES → Move to Foothold
    └── NO  → Try:
            - password spray
            - AS-REP roast
            - phishing (real world)
```

---

# 💻 3. FOOTHOLD (FIRST ACCESS)

Now you have creds.

## 🎯 GOAL: Get shell

---

### Check access:

```
crackmapexec smb <target> -u user -p pass
```

---

### Decision:

```
[SMB access?]
    ├── YES → psexec / smbexec
    └── NO

[WinRM open?]
    ├── YES → evil-winrm
    └── NO

[RDP open?]
    ├── YES → xfreerdp
```

---

# 🧠 4. INTERNAL ENUMERATION (THIS IS WHERE PROS WIN)

Once inside:

👉 STOP RUNNING RANDOM TOOLS  
👉 Start **structured enumeration**

---

## 🎯 GOAL: Find privilege escalation paths

---

## TOOLS:

- `winPEAS`
    
- `Seatbelt`
    
- `SharpHound` → BloodHound
    
- `PowerView`
    

---

## KEY THINGS TO LOOK FOR:

### 1. Privileges

```
whoami /priv
```

### 2. Groups

```
whoami /groups
```

### 3. Stored creds

```
cmdkey /list
```

### 4. Interesting files

- Desktop
    
- Documents
    
- Scripts
    

---

## 🧠 BLOODHOUND (CRITICAL THINKING TOOL)

```
SharpHound.exe
```

Upload → BloodHound

---

### You are now thinking like:

```
User → Group → Permission → Target
```

Example:

```
User → IT Support → Has Write on Server → Server runs as SYSTEM
```

---

# 🔺 5. PRIVILEGE ESCALATION

Now ask:

```
"What can THIS user control?"
```

---

## COMMON PATHS:

### 🔹 Weak Services

- Writable service binary
    
- Unquoted paths
    

### 🔹 Token Abuse

- SeImpersonatePrivilege → JuicyPotato
    

### 🔹 Credential Dumping

```
mimikatz
```

---

## DECISION:

```
[Local admin?]
    ├── YES → dump creds
    └── NO → escalate
```

---

# 🔁 6. LATERAL MOVEMENT

Once you get creds:

👉 Spread

---

## TOOLS:

- `crackmapexec`
    
- `psexec`
    
- `wmiexec`
    
- `evil-winrm`
    

---

## LOGIC:

```
[New creds found?]
    ├── YES → try on other machines
    └── NO → enumerate more
```

---

# 👑 7. DOMAIN DOMINATION

End goal:

- Domain Admin
    
- DCSync
    
- Golden Ticket
    

---

## PATHS:

### 🔹 DCSync Attack

```
mimikatz lsadump::dcsync
```

### 🔹 Pass-the-Hash

```
psexec.py -hashes
```

### 🔹 Kerberos Abuse

- Golden Ticket
    
- Silver Ticket
    

---

# 🔄 FULL MIND GRAPH (SIMPLIFIED)

```
Nmap
 ↓
Identify AD
 ↓
Enum (SMB, LDAP, Kerberos)
 ↓
[Creds?] ── NO → Roast / Spray / Enum more
 ↓ YES
Foothold (WinRM / SMB / RDP)
 ↓
Internal Enum (BloodHound, winPEAS)
 ↓
[PrivEsc?] ── NO → Enum more
 ↓ YES
Dump creds
 ↓
Lateral Movement
 ↓
[DA?] ── NO → Repeat loop
 ↓ YES
Domain Dominance
```

---

# 🧠 MASTER INSIGHT (THIS IS WHAT YOU WERE MISSING)

You don’t memorize tools — you map **situations → actions**:

|Situation|What you think|Tool|
|---|---|---|
|No creds|“Can I force auth?”|AS-REP|
|Have low user|“What services exist?”|Kerberoast|
|Inside machine|“What do I control?”|BloodHound|
|Got admin|“What creds exist?”|Mimikatz|
|Got new creds|“Where else works?”|CME|

---

# ⚡ FINAL MENTAL MODEL

Always ask:

1. **What do I have?**
    
2. **What can I access?**
    
3. **What can I control?**
    
4. **What can I escalate?**
    
5. **Where else can I go?**
    

---

# 🚀 If you want next level

I can give you:

- A **real-world attack walkthrough (HTB/THM style)**
    
- A **tool cheat sheet mapped to each phase**
    
- Or a **visual diagram you can print**
    

Just tell me.