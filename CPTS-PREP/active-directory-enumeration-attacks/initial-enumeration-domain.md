# 🛰️ Initial AD Environment Enumeration

## Overview [!ABSTRACT]
This guide outlines a systematic approach to enumerating an Active Directory (AD) environment, focusing on network discovery, user enumeration, and service detection. The objective is to identify key assets, vulnerabilities, and potential attack vectors for further exploitation.

## Network Discovery
### Passive Reconnaissance 🕵️‍♂️ [!INFO]
#### Tools
- **Wireshark:** For capturing SSL/TLS certificates.
- **Responder:** For detecting SMB/MS-NRPC service availability.

#### Commands
```bash
# Capture network traffic with Wireshark (ensure ens224 interface is set)
sudo wireshark -i ens224

# Respond to and capture NTLM hashes with Responder
sudo responder -I ens224 -A
```

### Active Discovery 🛠️ [!CHECK]
#### Fping for Host Detection
```bash
# Ping all hosts in the 172.16.5.x subnet (without DNS resolution)
fping -asgq 172.16.5.0/23

# Full network scan using Nmap with service detection enabled (-A) and aggressive timing (-T4)
sudo nmap -sn 172.16.5.0/23
```

#### Detailed Service Discovery 🛠️ [!CHECK]
```bash
# Perform a thorough port scan to identify open ports and services
sudo nmap -A -v -Pn TARGET_IP

# Generate an aggressive network-wide scan with output in various formats (XML, greppable, etc.)
sudo nmap -A -Pn -T5 -oA ./scan_results 172.16.5.0/23
```

### Example Network Scan Output 📊 [!EXAMPLE]
```plaintext
Nmap scan report for 172.16.5.5
Host is up (0.00048s latency).
Not shown: 995 closed tcp ports (reset)
PORT    STATE SERVICE VERSION
389/tcp open  ldap    Microsoft Windows Active Directory LDAP (Protocol-Dependent)
636/tcp open  tcpwrapped
135/tcp open  msrpc   Microsoft Windows RPC
445/tcp open  microsoft-ds Windows Server 2008 R2 x64 std-edition service pack 1
464/tcp open  kpasswd5?
593/tcp open  http-rpc-epmap Microsoft Windows HTTP services (ephominal)
...
```

### Service Detection 🛠️ [!CHECK]
```bash
# Use Nmap to identify specific versions of MS-SQL Server
sudo nmap -sV --script=ms-sql-info.nse 172.16.5.130

# Detect legacy Windows services that could be vulnerable
sudo nmap --script smb-vuln-ms17-010.nse 172.16.5.142
```

## User Enumeration 🔍 [!CHECK]
### Kerbrute Setup & Usage 🛠️ [!INFO]
#### Installation
```bash
# Clone the repository and compile for Linux
sudo git clone https://github.com/ropnop/kerbrute.git
cd kerbrute

# Compile for all platforms (Linux, macOS)
make all

# Install the binary to a system directory
sudo mv kerbrute_linux_amd64 /usr/local/bin/kerbrute
```

#### Username Enumeration Attack 🛠️ [!WARNING]
```bash
# Enumerate users against DC with wordlist
kerbrute userenum -d INLANEFREIGHT.LOCAL --dc 172.16.5.5 jsmith.txt -o valid_ad_users

# Example output:
[+] VALID USERNAME:       jjones@INLANEFREIGHT.LOCAL
[+] VALID USERNAME:       sbrown@INLANEFREIGHT.LOCAL  
...
```

## Key Data Points to Document 📝 [!INFO]
| Data Point | Description | Use Cases |
|------------|-------------|-----------|
| **AD Users** | Valid user accounts discovered | Password spraying, targeted attacks |
| **AD Computers** | Domain Controllers, file servers, SQL servers, web servers, Exchange | Service enumeration, lateral movement |
| **Key Services** | Kerberos, NetBIOS, LDAP, DNS | Protocol-specific attacks |
| **Vulnerable Hosts** | Legacy systems, unpatched services | Quick wins, privilege escalation |

---

## Paths to Domain Access 🚀 [!INFO]
### SYSTEM-Level Access Benefits
- Domain enumeration capabilities (computer account impersonation)
- Kerberoasting/ASREPRoasting attacks
- Net-NTLMv2 hash gathering with Inveigh
- SMB relay attacks
- Token impersonation for privileged accounts
- ACL attacks

### Common SYSTEM Access Methods
1. **Remote exploits:** MS08-067, EternalBlue, BlueKeep
2. **Service abuse:** SYSTEM services + SeImpersonate (Juicy Potato)
3. **Local privilege escalation:** Windows Task Scheduler 0-day
4. **Local admin + Psexec:** Launch SYSTEM cmd window

---

## Scanning Best Practices 🛠️ [!INFO]
### Operational Security Considerations
- Evasive vs Non-evasive: Understand engagement rules.
- Network impact: Some scans can destabilize systems.
- Industrial environments: Be cautious with sensors/controllers.

### Recommended Scan Approach
1. **Start passive:** Wireshark, Responder analysis.
2. **Light active:** fping, basic port scans.
3. **Targeted enumeration:** Focus on discovered services.
4. **Deep dive:** Service-specific enumeration tools.

---

## Lab Questions & Solutions 🧪 [!INFO]
### Question 1: CommonName of host 172.16.5.5
**Task:** Find the commonName in SSL certificate

#### Solution 🛠️
```bash
# SSH to attack host
ssh htb-student@10.129.226.51
# Password: HTB_@cademy_stdnt!

# Scan target host with service detection and OS/version determination
sudo nmap -A -v -Pn 172.16.5.5
```

**Answer:** `ACADEMY-EA-DC01.INLANEFREIGHT.LOCAL`

**Location:** Found in SSL-Cert details under port 3389 (RDP).

### Question 2: Host running Microsoft SQL Server 2019 15.00.2000.00
**Task:** Find IP address of host running specific SQL Server version

#### Solution 🛠️
```bash
# Network-wide scan with grepable output
sudo nmap -A -Pn -T5 -oG ./nmapOutput 172.16.5.0/23

# Extract SQL Server hosts
awk '/1433\/open/ {print $2}' nmapOutput

# Alternative: grep for SQL Server version
grep "Microsoft SQL Server 2019 15.00.2000.00" nmapOutput
```

**Answer:** `172.16.5.130`

**Location:** Found on port 1433 during service detection.

---

## Key Takeaways 📝 [!INFO]
1. **Methodical approach:** Passive → Active → Targeted enumeration.
2. **Documentation crucial:** Save all scan outputs for later analysis.
3. **Multiple tools:** Different tools reveal different information.
4. **Legacy systems:** High-value targets but require caution.
5. **User enumeration:** Critical for subsequent password attacks.
6. **Service focus:** Target AD-specific protocols (LDAP, Kerberos, DNS).

### Next Steps 🚀 [!INFO]
- Password spraying against enumerated users.
- Service-specific enumeration (SMB, LDAP, etc.).
- Vulnerability assessment of discovered hosts.
- Search for foothold opportunities.

### Useful Wordlists 🔍 [!INFO]
- **Usernames:** jsmith.txt, jsmith2.txt (from Insidetrust repository).
- **Passwords:** Common corporate passwords, season+year patterns.
- **Subdomain enumeration:** SecLists various wordlists.

---

## Command Reference 📜 [!INFO]

### Network Discovery
```bash
# Passive analysis
sudo wireshark -i ens224
sudo tcpdump -i ens224
sudo responder -I ens224 -A

# Active discovery  
fping -asgq 172.16.5.0/23
sudo nmap -sn 172.16.5.0/23

# Service enumeration
sudo nmap -A -v -Pn TARGET_IP
sudo nmap -A -Pn -T5 -oA scan_results 172.16.5.0/23
```

### User Enumeration
```bash
# Kerbrute setup
git clone https://github.com/ropnop/kerbrute.git
make all
sudo mv kerbrute_linux_amd64 /usr/local/bin/kerbrute

# Username enumeration
kerbrute userenum -d DOMAIN --dc DC_IP wordlist.txt -o valid_users
```

### Data Processing 🛠️ [!CHECK]
```bash
# Extract specific services
awk '/PORT_NUMBER\/open/ {print $2}' nmap_output.gnmap
grep "SERVICE_NAME" nmap_output

# Process and analyze data from Nmap output
cat nmap_output | grep -i "service"
```

---

This guide provides a structured approach to enumerating an AD environment, ensuring comprehensive coverage of network assets and potential attack vectors. [!ABSTRACT]