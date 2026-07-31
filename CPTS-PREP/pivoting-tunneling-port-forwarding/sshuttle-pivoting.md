# 🛰️ Introduction to SSHuttle for Network Pivoting

## 🔍 Overview of SSHuttle
[!ABSTRACT] SSHuttle is a Python-based tool that utilizes SSH to create dynamic, transparent tunnels between two networks, enabling network pivoting without the need for static port forwards or complex configurations.

---

## ⚙️ Installation and Basic Usage

### 📦 Installing SSHuttle
```bash
sudo apt-get install sshuttle
```

[!NOTE] Verify installation by checking version:
```bash
sshuttle --version
```

### 🔐 Authentication Methods
1. **Password**
   ```bash
   sudo sshuttle -r ubuntu@10.129.202.64 172.16.5.0/23
   ```
   
2. **Key Authentication**
   ```bash
   sudo sshuttle -r ubuntu@10.129.202.64 172.16.5.0/23 --ssh-cmd 'ssh -i key'
   ```

### 🌐 Network Connectivity Test
```bash
ping 172.16.5.19
```
[!SUCCESS] Expected output should show successful ICMP replies.

---

## ⚠️ Common Issues and Troubleshooting

### 🔒 Authentication Failures
```bash
# Problem: Connection refused or timed out
sudo sshuttle -r ubuntu@10.129.202.64 172.16.5.0/23

# Solutions:
1. Check SSH service status on pivot
   sudo systemctl status ssh

2. Use key authentication
   sudo sshuttle -r ubuntu@10.129.202.64 172.16.5.0/23 --ssh-cmd 'ssh -i key'

3. Check SSH service status
   sudo systemctl status ssh
```

### 🛠️ Network Routing Issues
```bash
# Problem: Cannot reach target network
Network unreachable

# Solutions:
1. Verify network range
   ip route show | grep 172.16.5

2. Test SSH server routing
   ssh ubuntu@10.129.202.64 'ip route'

3. Check target network existence
   ssh ubuntu@10.129.202.64 'ping 172.16.5.19'
```

### 🔄 iptables Cleanup Problems
```bash
# Problem: iptables rules not cleaned up
Rules persist after sshuttle exit

# Solutions:
1. Manual cleanup
   sudo iptables -t nat -F sshuttle-12300
   sudo iptables -t nat -X sshuttle-12300

2. Force kill and cleanup
   sudo pkill sshuttle
   sudo iptables -t nat -L | grep sshuttle

3. Restart networking
   sudo systemctl restart networking
```

---

## 🚀 Advanced Scenarios

### 🔗 Multiple Pivot Chains
```bash
# Chain multiple sshuttle connections
# Terminal 1: First pivot
sudo sshuttle -r user1@pivot1 10.0.0.0/8

# Terminal 2: Second pivot (through first)
sudo sshuttle -r user2@10.0.1.5 192.168.0.0/16 --ssh-cmd 'ssh -o ProxyCommand="ssh -W %h:%p user1@pivot1"'
```

### 🔁 Persistent sshuttle Service
```bash
# Create systemd service
sudo cat > /etc/systemd/system/sshuttle-pivot.service << EOF
[Unit]
Description=sshuttle pivot tunnel
After=network.target

[Service]
Type=simple
User=root
ExecStart=/usr/bin/sshuttle -r ubuntu@10.129.202.64 172.16.5.0/23
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF

# Enable and start service
sudo systemctl enable sshuttle-pivot
sudo systemctl start sshuttle-pivot
```

### 🌐 SSHuttle with SSH Tunnels
```bash
# Combine with local port forwards
ssh -L 8080:172.16.5.19:80 ubuntu@10.129.202.64 &
sudo sshuttle -r ubuntu@10.129.202.64 172.16.5.0/23

# Access both ways:
curl http://localhost:8080        # SSH local forward
curl http://172.16.5.19          # sshuttle routing
```

---

## 📈 Performance and Monitoring

### 🔧 Performance Optimization
```bash
# Enable compression for slow links
sudo sshuttle -r ubuntu@10.129.202.64 172.16.5.0/23 --ssh-cmd 'ssh -C'

# Adjust buffer sizes
sudo sshuttle -r ubuntu@10.129.202.64 172.16.5.0/23 --python /usr/bin/python3

# Use specific SSH cipher
sudo sshuttle -r ubuntu@10.129.202.64 172.16.5.0/23 --ssh-cmd 'ssh -c aes128-ctr'
```

### 🔍 Traffic Monitoring
```bash
# Monitor sshuttle traffic
sudo tcpdump -i any host 10.129.202.64

# Check iptables packet counts
sudo iptables -t nat -L sshuttle-12300 -v

# Monitor bandwidth usage
iftop -i eth0 -f "host 10.129.202.64"
```

### 📈 Resource Usage
```bash
# Check sshuttle processes
ps aux | grep sshuttle

# Monitor memory usage
top -p $(pgrep sshuttle)

# Check network connections
ss -tuln | grep :12300
```

---

## 🔐 Security Considerations

### 🛡️ Operational Security
[!WARNING] 
- **iptables Modifications**: detectable by system administrators
- **Process Visibility**: sshuttle processes visible in `ps` output
- **Network Traffic**: SSH connections to pivot hosts logged
- **DNS Queries**: may leak information if `--dns` used
- **Root Privileges**: requires elevated access

### 🛡️ Detection Mitigation
```bash
# Use non-standard SSH ports
sudo sshuttle -r ubuntu@10.129.202.64:2222 172.16.5.0/23

# Vary connection timing
sleep $((RANDOM % 300)); sudo sshuttle -r ubuntu@10.129.202.64 172.16.5.0/23

# Clean process names (limited effectiveness)
sudo sshuttle -r ubuntu@10.129.202.64 172.16.5.0/23 --python /usr/bin/python3
```

### 🛠️ Cleanup Procedures
```bash
# Proper shutdown
# Use Ctrl+C to stop sshuttle (auto-cleanup)

# Emergency cleanup
sudo pkill -f sshuttle
sudo iptables -t nat -F
sudo iptables -t nat -X

# Clear SSH known_hosts entries
ssh-keygen -R 10.129.202.64
```

---

## 🚀 Integration with Other Tools

### 🔐 Metasploit Integration
```ruby
# Use Metasploit normally with sshuttle active
msf6 > use auxiliary/scanner/portscan/tcp
msf6 auxiliary(scanner/portscan/tcp) > set RHOSTS 172.16.5.0/24
msf6 auxiliary(scanner/portscan/tcp) > run

# No proxy configuration needed!
```

### 🔐 Nmap Advanced Usage
```bash
# Full network scans through sshuttle
nmap -sS -A 172.16.5.0/24

# Service enumeration
nmap -sV -p- 172.16.5.19

# Vulnerability scanning
nmap --script vuln 172.16.5.19
```

### 🔐 Custom Applications
```bash
# Any TCP application works transparently
telnet 172.16.5.19 23
nc 172.16.5.19 445
python3 -c "import socket; s=socket.socket(); s.connect(('172.16.5.19', 80))"
```

---

## 📚 References

- **HTB Academy**: Pivoting, Tunneling & Port Forwarding - Page 9
- **sshuttle GitHub**: [Official Repository](https://github.com/sshuttle/sshuttle)
- **sshuttle Documentation**: [ReadTheDocs](https://sshuttle.readthedocs.io/)
- **Metasploit**: [Module Usage](https://metasploit.help.rapid7.com/docs/scanning-and-port-scanning)  
- **Nmap**: [Advanced Scans](https://nmap.org/book/man-scan-syntax.html)

---

This document provides a comprehensive guide to using SSHuttle for network pivoting, including installation, basic usage, troubleshooting, advanced scenarios, performance optimization, and security considerations. Additional references are provided for further reading. 

---


```bash
sudo sshuttle -r ubuntu@10.129.202.64 172.16.5.0/23 --python /usr/bin/python3
```

[!NOTE] Replace `ubuntu` and IP addresses with actual credentials and network ranges.

--- 

## 📜 Conclusion

SSHuttle is a powerful tool for network pivoting, offering dynamic tunneling capabilities that simplify the process of accessing remote networks. With proper setup and management, it can be an invaluable asset in various penetration testing scenarios. Understanding its intricacies and integrating it with other tools enhances its effectiveness significantly.

---

```bash
ping 172.16.5.19 -c 4
```

[!SUCCESS] Expected result: Successful ping replies indicate a functioning SSHuttle tunnel setup.
--- 

End of Document.