# 🛰️ Pivoting Techniques and Network Discovery

## 🔍 Proxychains Integration

Configure proxychains to use a SOCKS4 proxy:

```bash
echo "socks4 127.0.0.1 9050" >> /etc/proxychains.conf
```

Use tools through the proxy:

```bash
proxychains nmap -Pn -sT 172.16.5.19
proxychains msfconsole
proxychains xfreerdp /v:172.16.5.19 /u:victor /p:pass@123
```

[!INFO] Ensure `/etc/proxychains.conf` is properly configured with the correct proxy type and address.

## 🔍 Network Discovery

Check pivot interfaces:

```bash
ifconfig  # Linux
ipconfig /all  # Windows
```

Scan internal networks:

```bash
proxychains nmap -sn 172.16.5.1-200
proxychains nmap -Pn -sT -p 22,80,135,139,443,445,3389 172.16.5.19
```

[!WARNING] Ensure you have the necessary permissions to perform these scans.

## 🚀 Common Network Ranges

| **Range** | **Type** | **Description** |
|-----------|----------|-----------------|
| `10.0.0.0/8` | Private | Class A private networks |
| `172.16.0.0/12` | Private | Class B private networks |
| `192.168.0.0/16` | Private | Class C private networks |
| `169.254.0.0/16` | Link-Local | APIPA addresses |
| `127.0.0.0/8` | Loopback | Localhost |

## 🔍 Pivoting Opportunities Identification

### 🌐 Multi-homed Hosts

Check routes and network interfaces:

```bash
ip route show  # Linux
route print     # Windows
```

Show IP addresses and ARP tables:

```bash
ip addr show  # Linux
arp -a          # Linux/Windows
ipconfig /all   # Windows
```

### 🌐 Network Connectivity Testing

Test common private ranges:

```bash
ping -c 1 192.168.1.1
ping -c 1 10.10.10.1
ping -c 1 172.16.1.1
```

Check port connectivity:

```bash
nc -zv 192.168.1.100 22
telnet 172.16.5.19 3389
```

## 🚀 Service Discovery

Scan services through SOCKS proxy:

```bash
proxychains nmap -Pn -sT --top-ports 1000 172.16.5.0/24
proxychains masscan -p1-65535 --rate=1000 172.16.5.0/24
```

[!INFO] Use `-Pn` to treat all hosts as up without pinging.

## 🛠️ Tool Compatibility Matrix

| **Tool** | **SSH Tunnel** | **SOCKS Proxy** | **HTTP Tunnel** | **Notes** |
|----------|----------------|-----------------|-----------------|-----------|
| **Nmap** | ✅ (Local Forward) | ✅ (TCP Connect only) | ✅ | Use -sT scan type |
| **Metasploit** | ✅ | ✅ | ✅ | Full framework support |
| **Web Browsers** | ✅ | ✅ | ✅ | Configure proxy settings |
| **cURL/wget** | ✅ | ✅ | ✅ | Use --proxy flag |
| **Database Tools** | ✅ | ✅ | ✅ | Connect to forwarded ports |
| **RDP/VNC** | ✅ | ✅ | ✅ | Remote desktop access |

## 🔐 Security Considerations

### 🛡️ Operational Security (OPSEC)
1. Encrypt tunnels when possible (SSH, HTTPS).
2. Mimic legitimate traffic patterns.
3. Use standard ports when feasible (80, 443, 53).
4. Clean up connections after assessment.
5. Monitor tunnel stability and performance.

### 🛡️ Network Detection
- DPI may detect tunneling.
- Traffic analysis can reveal unusual patterns.
- Connection monitoring may alert on new services.
- Log correlation might expose pivot activities.

## ⚙️ Troubleshooting Guide

### 📄 Common Issues

| **Problem** | **Cause** | **Solution** |
|-------------|-----------|--------------|
| Connection timeout | Firewall blocking | Try different ports/protocols |
| DNS resolution fails | DNS not proxied | Enable proxy_dns in proxychains |
| Slow performance | Network latency | Use compression (-C flag) |
| Tool incompatibility | Partial packet support | Use TCP connect scans only |

### 🔍 Debugging Commands

Check tunnel status:

```bash
netstat -antp | grep :9050
ss -tlnp | grep :9050
```

Test connectivity:

```bash
nc -v 127.0.0.1 9050
telnet 127.0.0.1 9050
```

Verbose output:

```bash
proxychains -v nmap target
ssh -v -D 9050 user@pivot
```

## 🎮 Lab Environment Setup

### 🔍 HTB Academy Lab Scenario

**Credentials:**
- Ubuntu Server: `ubuntu:HTB_@cademy_stdnt!`
- Windows Target: `victor:pass@123`

**Network Topology:**

```
Attack Host → Ubuntu Server (10.129.202.64) → Windows DC (172.16.5.19)
            ens192: 10.129.202.64       ens224: 172.16.5.129
```

**Objectives:**
1. Enumerate network interfaces on pivot.
2. Set up SOCKS proxy via SSH.
3. Scan internal network through proxy.
4. Access Windows host via RDP.
5. Retrieve flag from Desktop.

## 📝 Best Practices Checklist

### 🔍 Pre-Assessment
- [ ] Map network topology.
- [ ] Identify trust relationships.
- [ ] Locate multi-homed hosts.
- [ ] Test basic connectivity.

### 🔍 During Assessment
- [ ] Use encrypted tunnels.
- [ ] Monitor connection stability.
- [ ] Document tunnel configurations.
- [ ] Test tool compatibility.

### 🔍 Post-Assessment
- [ ] Clean up all connections.
- [ ] Remove configuration files.
- [ ] Document findings.
- [ ] Verify cleanup completion.

## 📘 Exam Tips for CPTS

### 🔍 Key Skills to Master
1. Quick tunnel setup under time pressure.
2. Tool integration through proxies.
3. Multi-hop scenarios planning.
4. Troubleshooting common issues.
5. Documentation of pivot paths.

### 🔍 Practice Scenarios
- Set up tunnels in under 2 minutes.
- Chain multiple pivots successfully.
- Use various tools through proxies.
- Handle connection failures gracefully.
- Maintain operational security.

## 🏁 Next Steps

1. Start with Dynamic Port Forwarding: Review HTB Academy Page 3 concepts.
2. Practice SSH Tunneling: Master all forwarding types.
3. Learn Proxychains: Configure and use with various tools.
4. Explore Modern Tools: Chisel and Ligolo-ng alternatives.
5. Complete Skills Assessment: Hands-on lab scenarios.

## 📚 References

- **HTB Academy**: Pivoting, Tunneling & Port Forwarding Module
- **SSH Documentation**: `man ssh`, `man ssh_config`
- **Proxychains**: `/etc/proxychains.conf` configuration
- **SOCKS Protocol**: RFC 1928 (SOCKS5), RFC 1929 (Authentication)
- **Network Fundamentals**: RFC 1918 (Private Address Space)