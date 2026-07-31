---
# 🛠️ Hyper-V Administrators Privilege Escalation

> [!ABSTRACT] Overview of Hyper-V Administrators Group Risks
> 
> Members have full access to all Hyper-V features and can potentially escalate privileges to Domain Admin level if Domain Controllers are virtualized.

## 🖥️ Virtual Machine Attack Vectors

### Domain Controller VM Compromise

```cmd
# Attack scenario:
1. Create clone of live Domain Controller VM
2. Mount virtual disk (.vhdx) offline
3. Extract NTDS.dit from mounted filesystem
4. Use secretsdump.py for credential extraction
```

> [!WARNING] Full Domain Compromise Potential
> Virtualized DCs pose a significant risk due to the ability to bypass online protections and remain undetected.

## 🔗 Hard Link Exploitation

### Attack Mechanism

```cmd
# CVE-2018-0952 / CVE-2019-0841 exploitation:
1. vmms.exe restores permissions as NT AUTHORITY\SYSTEM
2. Delete target .vhdx file
3. Create hard link to protected SYSTEM file
4. Gain full permissions on SYSTEM file
```

### Target File Example

```cmd
# Mozilla Maintenance Service target
C:\Program Files (x86)\Mozilla Maintenance Service\maintenanceservice.exe
```

### Exploitation Steps

```cmd
# 1. Run PowerShell hard link exploit
# 2. Take ownership of target file
takeown /F "C:\Program Files (x86)\Mozilla Maintenance Service\maintenanceservice.exe"

# 3. Replace with malicious executable
# 4. Start service for SYSTEM execution
sc.exe start MozillaMaintenance
```

> [!SUCCESS] Successful Exploitation Steps

## ⚠️ Limitations

### Patching Status

```cmd
# MITIGATED: March 2020 Windows security updates
# Changed hard link behavior
# Technique no longer effective on patched systems
```

> [!CAUTION] Ensure Systems Are Up-to-Date
> Hard link exploitation is mitigated in patched systems.

### Alternative Vectors

```cmd
# Focus on:
- VM-based attacks (still viable)
- Service exploitation requiring SYSTEM context
- Application services startable by unprivileged users
```

## 🔍 Detection & Defense

### Monitoring

```cmd
# Watch for:
- Hyper-V VM cloning activities
- Unexpected VM creation/deletion
- Hard link creation attempts
- Service file modifications
```

> [!CHECK] Verification Steps to Monitor

### Hardening

```cmd
# Mitigation strategies:
- Regular Windows updates (March 2020+)
- Restrict Hyper-V Administrators membership
- Monitor VM operations
- Implement VM integrity checking
```

## 💡 Key Takeaways

1. **Hyper-V Administrators** = potential Domain Admin access on virtualized DCs.
2. **VM cloning attack** most reliable vector.
3. **Hard link exploitation** patched since March 2020.
4. **Virtualization security** critical for domain protection.

---

> [!ABSTRACT] Importance of Virtualization Security
>
*Hyper-V Administrators group represents significant risk in virtualized environments, particularly when Domain Controllers are virtualized.*
---