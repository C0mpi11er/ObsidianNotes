# 🌡️ Example Medium Finding - Insecure File Shares

---

## 🔍 Initial Observation

> [!NOTE] 
> This finding identifies insecurely configured file shares that may expose sensitive data to unauthorized access.

---

### ⚠️ Insecure Share Identification

Identified file share `\\<Target_IP>\DataShare` is accessible without authentication. The share contains sensitive documents such as financial reports and employee information.

---

## 🛠️ Verification Steps

> [!CHECK] 
> Verifying if the identified shares are writable and if they contain any high-value files:

```bash
smbclient -N -L //<Target_IP>
```

Accessing the share without authentication confirms that it is readable. Further investigation reveals:

```bash
smbclient //<Target_IP>/DataShare -c "ls"
```

---

## 🔑 Exploitation

### Enumeration and Credential Harvesting

> [!SUCCESS] 
> Using [[CrackMapExec]] to enumerate users, groups, and credentials from the compromised share.

```bash
crackmapexec smb <Target_IP> \
-u 'users.txt' \
-p 'passwords.txt'
```

---

## 💡 Additional Recommendations

- **Restrict Share Access**: Implement granular permissions on file shares.
- **Enable SMB Signing**: Prevent NTLM Relay attacks by enabling SMB signing.

---

## 📈 Impact Assessment

| Finding | Description |
|---|---|
| Insecure File Shares | Exposes sensitive data to unauthorized users. |

> [!WARNING] 
> Unauthorized access to these files could lead to a significant breach of confidentiality, integrity, and availability (CIA) for the organization.

---

## 🧠 Mental Model

```text
File Share Exposure → Data Access → Lateral Movement → Credential Harvesting → Privilege Escalation
```

This mental model helps in understanding how exposed file shares can be leveraged to gain deeper access within a network, leading to further exploitation and compromise.