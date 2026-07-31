# 🛰️ HTB Academy Pivoting, Tunneling & Port Forwarding

## 🔍 Lab Exercise Overview
[!INFO] The task involves setting up an SSH tunnel using Plink from a Windows-based attack host to pivot through a Linux machine (10.129.202.64) and subsequently RDP into an internal Windows target (172.16.5.19). Use the credentials 'victor:pass@123'.

## 🚀 Lab Exercise Recreation

### ✅ HTB Academy Optional Exercise
[!SUCCESS] Task completed by setting up a SOCKS tunnel through Plink and RDP into the internal Windows target.

#### 🛠️ Environment Setup
```cmd
# Requirements:
- Windows attack host
- Plink.exe available
- Network access to 10.129.202.64 (pivot)
- Target: 172.16.5.19 (internal Windows)
```

#### 🔒 Establishing the Plink Tunnel
```cmd
# Create SOCKS tunnel through Ubuntu pivot
plink -ssh -D 9050 ubuntu@10.129.202.64

# Enter password when prompted
ubuntu@10.129.202.64's password: HTB_@cademy_stdnt!
```

#### 🌐 Configuring Proxifier
[!NOTE] Configure the SOCKS proxy in Proxifier to route RDP traffic through the tunnel.
```
1. Open Proxifier
2. Profile → Proxy Servers → Add
   - Address: 127.0.0.1
   - Port: 9050
   - Type: SOCKS4
3. Profile → Proxification Rules → Add
   - Applications: mstsc.exe
   - Target Hosts: 172.16.5.19
   - Action: Proxy 127.0.0.1:9050
```

#### 💻 RDP Connection
```cmd
# Launch Remote Desktop
mstsc.exe

# Connection details:
Computer: 172.16.5.19
User name: victor
Password: pass@123
```

#### 📝 Submitting the Answer
[!SUCCESS] "I tried Plink"

## 🔍 Comparison with Linux SSH Methods

### 🔢 Functionality Comparison
| **Feature** | **Linux SSH** | **Windows Plink** |
|-------------|---------------|-------------------|
| **Dynamic Forward** | `ssh -D 9050` | `plink -ssh -D 9050` |
| **Local Forward** | `ssh -L 8080:target:80` | `plink -ssh -L 8080:target:80` |
| **Remote Forward** | `ssh -R 8080:localhost:80` | `plink -ssh -R 8080:localhost:80` |
| **Background** | `ssh -fN -D 9050` | `start /B plink -ssh -D 9050` |
| **Key Auth** | `ssh -i key` | `plink -i key.ppk` |

### 🛠️ Integration Differences

#### 🔧 Linux Integration
```bash
# Direct proxychains support
proxychains nmap -sT 172.16.5.19

# Built-in SOCKS applications
curl --socks5 127.0.0.1:9050 http://172.16.5.19
```

#### 🛠️ Windows Integration
```cmd
# Requires Proxifier for most applications
Proxifier → mstsc.exe → 172.16.5.19

# Some native SOCKS support
firefox → proxy settings → SOCKS 127.0.0.1:9050
```

## 🚀 Real-World Scenarios

### 🔐 Scenario 1: Corporate Windows Environment
[!WARNING] Pentesting corporate network with existing Plink setup.
```
Situation: Pentesting corporate network
Environment: Windows workstations with PuTTY installed
Goal: Pivot through DMZ host to internal network
Solution: Use Plink for SOCKS tunneling + Proxifier for RDP
```

### 🔐 Scenario 2: Legacy System Compromise
[!NOTE] Leveraging existing tools in a compromised system.
```
Situation: Compromised older Windows server
Limitation: Cannot upload new tools
Available: PuTTY suite installed for administration
Solution: Leverage existing Plink for tunneling
```

### 🔐 Scenario 3: Windows Red Team Operation
[!SUCCESS] Using native tools to blend in.
```
Situation: Windows-based red team infrastructure
Challenge: Need to blend in with Windows environment
Approach: Use Windows-native tools (Plink, Proxifier, mstsc)
Benefit: Reduced detection, natural tool usage
```

## 🔍 Best Practices

### 🛠️ Operational Guidelines
1. **Test Locally First** - Verify Plink works before deployment.
2. **Multiple Tunnels** - Create redundant paths when possible.
3. **Authentication Security** - Use keys when possible.
4. **Clean Exit** - Properly terminate sessions.
5. **Documentation** - Record tunnel configurations.

### 🔒 Security Recommendations
1. **Timing Variation** - Don't establish tunnels at predictable times.
2. **Port Diversity** - Use different SOCKS ports.
3. **Session Management** - Monitor and limit session duration.
4. **Log Cleanup** - Clear relevant Windows event logs.
5. **Process Hiding** - Consider process migration techniques.

### 🚀 Performance Optimization
1. **Compression** - Use SSH compression for slow links.
2. **Keep-Alive** - Maintain persistent connections.
3. **Concurrent Sessions** - Balance load across multiple tunnels.
4. **Bandwidth Monitoring** - Track usage patterns.

## 🔍 Integration with Other Tools

### 🛠️ Metasploit Integration
```ruby
# Metasploit with SOCKS proxy (requires Proxychains4Windows)
msf6 > setg Proxies socks4:127.0.0.1:9050
msf6 auxiliary(scanner/portscan/tcp) > run
```

### 🛠️ PowerShell Integration
```powershell
# PowerShell with proxy settings
$proxy = New-Object System.Net.WebProxy("socks://127.0.0.1:9050")
$webClient = New-Object System.Net.WebClient
$webClient.Proxy = $proxy
$webClient.DownloadString("http://172.16.5.19")
```

### 🛠️ Nmap through Proxy
```cmd
# Using ProxyChains4Windows (if available)
proxychains4 nmap -sT -Pn 172.16.5.19

# Alternative: nmap with HTTP proxy (if SOCKS-to-HTTP converter used)
nmap --proxy socks4://127.0.0.1:9050 172.16.5.19
```

---

## 📚 References

- **HTB Academy**: Pivoting, Tunneling & Port Forwarding - Page 8
- **PuTTY Documentation**: [Official PuTTY Manual](https://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html)
- **Proxifier Manual**: [Proxifier Documentation](https://www.proxifier.com/documentation/)
- **SANS**: [SSH Tunneling with Windows](https://www.sans.org/blog/ssh-tunneling-with-windows/)
- **Microsoft**: [Windows SSH Client](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse)