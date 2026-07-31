# 🛰️ You Security Assessment

## Enumeration Checklist

### RDP Enumeration
- [ ] Port scan for RDP (3389/tcp)
- [ ] Version detection and banner grabbing
- [ ] Certificate analysis
- [ ] Encryption enumeration
- [ ] Authentication testing
- [ ] Vulnerability scanning
- [ ] Brute force protection testing

### WinRM Enumeration
- [ ] Port scan for WinRM (5985,5986/tcp)
- [ ] Service detection and version identification
- [ ] Authentication method enumeration
- [ ] HTTP/HTTPS configuration analysis
- [ ] Command execution testing
- [ ] PowerShell remoting testing
- [ ] Configuration analysis

### WMI Enumeration
- [ ] Port scan for RPC (135/tcp)
- [ ] Service detection and enumeration
- [ ] Authentication testing
- [ ] Information gathering via WMI queries
- [ ] Access control testing
- [ ] Privilege assessment
- [ ] Persistence mechanism analysis

## Attack VMIectors

### RDP Attack Vectors
```bash
# RDP brute force
hydra -l administrator -P passwords.txt rdp://target

# RDP vulnerability exploitation
# BlueKeep (CVE-2019-0708)
# DejaBlue (CVE-2019-1181, CVE-2019-1182)

# RDP credential harvesting
# Keyloggers in RDP sessions
# Clipboard monitoring
```

### WinRM Attack Vectors
```bash
# WinRM command execution
evil-winrm -i target -u username -p password

# WinRM PowerShell exploitation
Enter-PSSession -ComputerName target -Credential username
Invoke-Command -ComputerName target -ScriptBlock {whoami}

# WinRM persistence
# Event subscriptions via WMI
# Scheduled tasks
```

### WMI Attack Vectors
```bash
# WMI command execution
wmic /node:target process call create "cmd.exe /c command"

# WMI persistence
# Event subscriptions
# MOF files
# WMI classes

# WMI lateral movement
# Remote process creation
# Service manipulation
```

## Common Vulnerabilities

### RDP Vulnerabilities
- **CVE-2019-0708**: BlueKeep RCE vulnerability
- **CVE-2019-1181**: DejaBlue RCE vulnerability
- **CVE-2019-1182**: DejaBlue RCE vulnerability
- **CVE-2012-0002**: RDP denial of service
- **CVE-2018-0886**: CredSSP authentication bypass

### WinRM Vulnerabilities
- **Configuration Issues**: Weak authentication settings
- **Network Exposure**: WinRM accessible from untrusted networks
- **Authentication Bypass**: Weak authentication mechanisms
- **Privilege Escalation**: WinRM-based escalation techniques

### WMI Vulnerabilities
- **WMI Event Subscriptions**: Persistence mechanisms
- **WMI Query Injection**: Malicious WQL queries
- **Access Control**: Insufficient WMI permissions
- **Information Disclosure**: Sensitive system information

## Tools and Techniques

### RDP Tools
```bash
# RDP clients
mstsc                # Windows Remote Desktop
rdesktop             # Linux RDP client
xfreerdp             # Cross-platform RDP client
freerdp              # Free RDP implementation

# RDP security tools
nmap                 # Network scanning
hydra                # Brute force
ncrack               # Network authentication cracker
```

### WinRM Tools
```bash
# WinRM clients
winrs                # Windows Remote Shell
evil-winrm           # WinRM pentesting tool
pwsh                 # PowerShell Core

# WinRM testing tools
crackmapexec         # Network authentication testing
nmap                 # Service detection
```

### WMI Tools
```bash
# WMI clients
wmic                 # Windows WMI command-line
powershell           # PowerShell WMI cmdlets
wmios                # WMI object browser

# WMI testing tools
wmiexec              # WMI command execution
wmipersist           # WMI persistence toolkit
```

## Defensive Measures

### RDP Hardening
```bash
# Change default RDP port
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v PortNumber /t REG_DWORD /d 3390 /f

# Enable Network Level Authentication
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

# Restrict RDP access
# Use Group Policy to limit RDP access
# Configure firewall rules
```

### WinRM Security
```bash
# Disable WinRM if not needed
Stop-Service winrm
Set-Service winrm -StartupType Disabled

# Configure WinRM securely
winrm set winrm/config/service/auth @{Basic="false"}
winrm set winrm/config/service @{AllowUnencrypted="false"}

# Restrict WinRM access
# Use Group Policy to configure WinRM
# Configure firewall rules
```

### WMI Security
```bash
# Configure WMI security
# Use Group Policy to configure WMI settings
# Set appropriate DCOM permissions
# Monitor WMI activity

# Disable WMI if not needed
Stop-Service winmgmt
Set-Service winmgmt -StartupType Disabled
```

## Best Practices

### RDP Best Practices
1. **Change default port**: Use non-standard ports
2. **Enable NLA**: Require Network Level Authentication
3. **Use strong passwords**: Implement password policies
4. **Limit access**: Restrict RDP access to authorized users
5. **Monitor connections**: Log and monitor RDP sessions
6. **Keep updated**: Apply security patches regularly

### WinRM Best Practices
1. **Use HTTPS**: Enable SSL/TLS encryption
2. **Restrict authentication**: Disable basic authentication
3. **Limit access**: Configure trusted hosts carefully
4. **Monitor activity**: Log WinRM connections and commands
5. **Network security**: Use firewall rules and VPNs
6. **Regular audits**: Review WinRM configuration regularly

### WMI Best Practices
1. **Access control**: Set appropriate WMI permissions
2. **Monitor activity**: Log WMI queries and changes
3. **Disable if unused**: Turn off WMI if not needed
4. **Regular audits**: Review WMI configuration and usage
5. **Network security**: Restrict WMI network access
6. **Update regularly**: Keep WMI components updated

## Detection and Monitoring

### RDP Monitoring
```bash
# Monitor RDP connections
# Windows Event Logs: Security, TerminalServices-LocalSessionManager
# Event IDs: 4624, 4625, 1149

# RDP connection logging
auditpol /set /subcategory:"Logon" /success:enable /failure:enable
```

### WinRM Monitoring
```bash
# Monitor WinRM activity
# Windows Event Logs: Microsoft-Windows-WinRM
# PowerShell logging: Module, ScriptBlock, Transcription

# WinRM logging configuration
winrm set winrm/config/service @{EnableCompatibilityHttpListener="true"}
```

### WMI Monitoring
```bash
# Monitor WMI activity
# Windows Event Logs: Microsoft-Windows-WMI-Activity
# Event IDs: 5857, 5858, 5859, 5860, 5861

# WMI logging configuration
# Enable WMI-Activity logging via Group Policy
``` 
---