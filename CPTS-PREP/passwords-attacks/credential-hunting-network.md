# 🛰️ Network Traffic Credential Hunting Guide

## 🔍 Introduction to Credential Extraction Techniques

### Overview of Wireshark and Pcredz

Wireshark is an indispensable tool for network traffic analysis, enabling detailed inspection of protocols like HTTP, FTP, SMTP, and others. Pcredz is a Python script that automates the process of extracting credentials from Wireshark packet captures (pcap files).

[!INFO] For this guide, we will focus on analyzing `demo.pcapng` for credential extraction using both Wireshark and Pcredz.

---

## 🔎 Protocol Analysis and Credential Hunting

### HTTP/HTTPS Analysis

#### Basic Authentication
```bash
# Wireshark filters
http.request.method == "GET"        # Look for GET requests with credentials
http.request.uri contains "login"   # Login forms

# Wireshark analysis steps:
1. Apply the filter `http.request.uri contains "login"`
2. Inspect HTTP headers for Authorization: Basic base64(username:password)
3. Decode base64 using online tools or command line utilities (e.g., echo -n 'user:pass' | openssl base64)

# Pcredz analysis
python3 ./Pcredz -f demo.pcapng -v
```

### SMB/NTLM Analysis

#### NTLM Authentication Sequence
```plaintext
1. Type 1 Message (Negotiate)
2. Type 2 Message (Challenge)
3. Type 3 Message (Authentication) ← Contains NTLM hash
```

[!WARNING] Be cautious with SMB and NTLM hashes, as they can be used for offline cracking.

### FTP Analysis

#### FTP Command Sequence
```bash
# Wireshark filters
ftp.request.command == "USER"
ftp.request.command == "PASS"
ftp.response.code == 230        # Login successful

# Typical FTP login sequence
USER admin
PASS secretpassword
230 Login successful
```

#### FTP Data Analysis
```bash
# Track file transfers
ftp.request.command == "RETR"   # Downloads
ftp.request.command == "STOR"   # Uploads
ftp.request.command == "LIST"   # Directory listings

# Follow FTP data channel
tcp.port == 20                  # FTP data port
```

### SNMP Community String Extraction
```bash
# Wireshark filter
snmp

# Common community strings
- public (read-only)
- private (read-write)
- admin
- community
- secret

# SNMP version analysis
- SNMPv1: Community string in plaintext
- SNMPv2c: Community string in plaintext  
- SNMPv3: Encrypted (secure)
```

### Email Protocol Analysis

#### POP3 Credential Extraction
```bash
# Wireshark filter
pop

# POP3 authentication sequence
USER username
PASS password
+OK Login successful

# Common POP3 commands
USER, PASS, STAT, LIST, RETR, DELE, QUIT
```

#### SMTP Authentication
```bash
# Wireshark filter
smtp

# SMTP AUTH sequence
AUTH LOGIN
334 VXNlcm5hbWU6          # Username: (base64)
334 UGFzc3dvcmQ6          # Password: (base64)
235 Authentication successful
```

## 🕵️ Advanced Network Hunting Techniques

### Network Reconnaissance from Traffic
```bash
# DNS Analysis
dns                           # All DNS traffic
dns.qry.name contains "internal"  # Internal domain queries
dns.qry.name contains "admin"     # Admin-related queries

# Network mapping from traffic
ip.addr == 192.168.0.0/16    # Internal networks
arp                           # ARP requests (network discovery)
icmp.type == 8                # Ping requests
```

### Wireless Network Credential Hunting
```bash
# WiFi authentication analysis
wlan.fc.type_subtype == 0x0b  # Authentication frames
eapol                         # WPA/WPA2 handshakes

# WPA handshake capture
1. Capture 4-way handshake
2. Extract to .hccapx format
3. Crack with hashcat
```

### VPN and Tunneled Traffic
```bash
# IPSec analysis
esp                           # Encrypted Security Payload
isakmp                        # IKE negotiations

# OpenVPN detection
udp.port == 1194
tcp.port == 443 && ssl        # SSL VPN
```

## 🎯 HTB Academy Lab Exercise

### Lab Setup
- **Objective**: Analyze `demo.pcapng` for credential extraction.
- **Tools**: Wireshark and Pcredz.
- **Target Information**: Mixed network traffic with cleartext credentials.

### Lab Questions and Analysis

#### Question 1: Credit Card Information
**Objective**: Find cleartext credit card number.
**Analysis approach**:
```bash
# Wireshark analysis
http contains "4"             # Look for card numbers starting with 4
frame contains "credit"       # Search for credit-related terms
http.request.method == "POST" # Payment forms

# Pcredz analysis
python3 ./Pcredz -f demo.pcapng -v
# Look for: "CC number scanning activated"
```

#### Question 2: SNMPv2 Community String
**Objective**: Extract SNMP community string.
**Analysis approach**:
```bash
# Wireshark analysis
snmp                          # Filter SNMP traffic
snmp.community               # Community string field

# Pcredz analysis
python3 ./Pcredz -f demo.pcapng -v
# Look for: "Found SNMPv2 Community string"
```

#### Question 3: FTP Password
**Objective**: Find FTP login password.
**Analysis approach**:
```bash
# Wireshark analysis
ftp.request.command == "PASS" # FTP password commands
tcp.stream eq X               # Follow FTP conversation

# Pcredz analysis
python3 ./Pcredz -f demo.pcapng -v
# Look for: "FTP Pass:"
```

#### Question 4: Downloaded File
**Objective**: Identify file downloaded via FTP.
**Analysis approach**:
```bash
# Wireshark analysis
ftp.request.command == "RETR" # File retrieval commands
ftp                           # Follow FTP data stream

# Manual analysis
1. Find FTP login sequence
2. Look for RETR commands
3. Note filename in command
```

### Systematic Analysis Workflow
```bash
# Step 1: Open pcap in Wireshark
wireshark demo.pcapng

# Step 2: Protocol hierarchy analysis
Statistics → Protocol Hierarchy

# Step 3: Filter for credentials
http contains "password"
ftp
snmp

# Step 4: Run Pcredz analysis
python3 ./Pcredz -f demo.pcapng -v -t

# Step 5: Follow interesting TCP streams
Right-click → Follow → TCP Stream

# Step 6: Export specific data if needed
File → Export Objects → HTTP/FTP
```

## 📋 Network Credential Hunting Checklist

### Pre-Analysis Setup
- [ ] Identify capture source and timeframe.
- [ ] Check file integrity and size.
- [ ] Review capture filters used.
- [ ] Understand network topology.

### Protocol Analysis
- [ ] HTTP traffic for forms and Basic Auth.
- [ ] FTP for username/password sequences.
- [ ] SNMP for community strings.
- [ ] Email protocols (POP3/IMAP/SMTP).
- [ ] Telnet for cleartext sessions.
- [ ] LDAP for bind credentials.

### Automated Analysis
- [ ] Run Pcredz with verbose output.
- [ ] Check for credit card patterns.
- [ ] Extract NTLM hashes.
- [ ] Identify Kerberos tickets.
- [ ] Parse authentication headers.

### Manual Verification
- [ ] Verify automated findings.
- [ ] Follow relevant TCP streams.
- [ ] Decode base64 credentials.
- [ ] Cross-reference timestamps.
- [ ] Document credential context.

### Reporting
- [ ] Catalog all discovered credentials.
- [ ] Note protocols and timestamps.
- [ ] Assess credential strength.
- [ ] Identify affected systems.
- [ ] Recommend remediation.

## 🛡️ Detection and Prevention

### Network Security Recommendations
```bash
# Protocol Migration
HTTP → HTTPS                  # Implement TLS certificates
FTP → SFTP/FTPS              # Use secure file transfer
Telnet → SSH                 # Replace with encrypted shell
SNMP v1/v2c → SNMPv3         # Enable SNMP encryption
POP3/IMAP → POP3S/IMAPS      # Enable email encryption
```

### Network Monitoring
```bash
# Detect credential hunting activities
- Promiscuous mode detection.
- Unusual packet capture patterns.
- Network sniffing tool signatures.
- Abnormal traffic analysis queries.
```

## 💡 Key Takeaways

1. **Legacy protocols** - Many environments still use unencrypted protocols.
2. **Wireshark mastery** - Essential for network traffic analysis.
3. **Pcredz efficiency** - Automates credential extraction from captures.
4. **Protocol knowledge** - Understanding authentication flows is crucial.
5. **Stream analysis** - Following TCP conversations reveals full context.
6. **Pattern recognition** - Learn to identify credential-bearing traffic.
7. **Automated tools** - Combine manual analysis with automated extraction.
8. **Defense awareness** - Recommend encrypted alternatives.

---

*This guide provides comprehensive network traffic credential hunting techniques using Wireshark and Pcredz, based on HTB Academy's Password Attacks module.*

---