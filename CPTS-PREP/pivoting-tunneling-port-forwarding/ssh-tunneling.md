# 🛰️ SSH Tunneling Guide

## Introduction
SSH tunneling allows secure connections between multiple networks, providing a way to access internal services over an encrypted link. This guide covers various aspects of setting up and managing SSH tunnels.

---

## **Basics of SSH Tunneling**

### **Local Forwarding**
[!INFO] Local forwarding maps a local port on your machine (listening) to an address and port on the remote host.
```bash
# Map 8080 on localhost to HTTP on target
ssh -L 8080:target:80 user@pivot.com

# Forward MySQL traffic through SSH
ssh -L 3306:mysql.internal.corp:3306 user@jumpbox.corp.com

# Use non-default port and background mode
ssh -fN -L 1433:sql.internal.corp:1433 -p 2222 user@pivot.com
```

### **Remote Forwarding**
[!INFO] Remote forwarding maps a remote port on the target machine (listening) to an address and port on your local machine.
```bash
# Map 8080 on pivot server to HTTP on localhost
ssh -R 8080:localhost:80 user@pivot.com

# Forward RDP traffic from internal network to pivot point
ssh -fNT -R 3389:192.168.1.50:3389 user@jumpbox.corp.com
```

### **SOCKS Proxy**
[!INFO] SOCKS proxy allows your machine to use SSH as a socks proxy.
```bash
# Use pivot server as SOCKS proxy (port 1080)
ssh -D 1080 user@pivot.com

# Configure Firefox for SOCKS proxy on localhost:1080
```

### **Dynamic Port Forwarding**
[!INFO] Dynamic port forwarding can be used with applications like Burp Suite.
```bash
# Map local port to internal RDP server and set up a dynamic forward
ssh -L 3389:windows.internal.corp:3389 -D 1080 user@jumpbox.corp.com

# Use Burp Suite as a proxy
firefox --proxy-server="socks5://localhost:1080" http://internal-web.corp.com
```

---

## **Examples of SSH Tunneling**

### **Example Scenario: Web Application Testing**
[!EXAMPLE] Set up tunnel to test an internal web application using Burp Suite.
```bash
# Forward HTTP and HTTPS traffic through the tunnel
ssh -L 8080:http.internal.corp.com:80 -L 443:https.internal.corp.com:443 user@jumpbox.corp.com

# Set up a SOCKS proxy for full control over network requests
ssh -D 1080 user@jumpbox.corp.com

# Configure Burp Suite to use the SOCKS proxy
```

### **Example Scenario: Database Access**
[!EXAMPLE] Securely access internal SQL Server and MySQL databases.
```bash
# Forward port for SQL Server database
ssh -L 1433:sql.internal.corp:1433 user@jumpbox.corp.com

# Connect to SQL Server with sqlcmd or similar tool
sqlcmd -S localhost,1433 -U sa -P password

# Forward MySQL traffic and connect using the forwarded port
ssh -L 3306:mysql.internal.corp:3306 user@jumpbox.corp.com
mysql -h 127.0.0.1 -P 3306 -u root -p
```

### **Example Scenario: Remote Desktop/VNC Access**
[!EXAMPLE] Set up RDP and VNC access to internal Windows/Linux machines.
```bash
# Forward RDP traffic for remote desktop access
ssh -L 3389:windows.internal.corp:3389 user@jumpbox.corp.com

# Connect with rdesktop or similar tool
rdesktop localhost:3389

# Forward VNC port and connect using a VNC client
ssh -L 5900:linux.internal.corp:5900 user@jumpbox.corp.com
vncviewer localhost:5900
```

---

## **Advanced SSH Tunneling**

### **Multiple Hops (ProxyJump)**
[!INFO] Use multiple hop configuration to access final target through intermediate hosts.
```bash
# SSH through multiple hops
ssh -J user1@hop1.com,user2@hop2.com user3@final-target.com

# Port forward through multiple hops
ssh -J user@pivot1.com -L 8080:internal.local:80 user@pivot2.com
```

### **SSH Config File**
[!INFO] Configure `~/.ssh/config` for easier tunnel setup.
```bash
# ~/.ssh/config file example
Host pivot
    HostName 10.10.10.50
    User pentester
    Port 22
    LocalForward 8080 192.168.1.100:80
    LocalForward 3389 192.168.1.50:3389
    DynamicForward 1080

# Usage
ssh pivot
```

### **Persistent Tunnels with autossh**
[!INFO] Use `autossh` to maintain persistent SSH tunnels.
```bash
# Install autossh for persistent connections
apt install autossh

# Persistent tunnel that automatically reconnects
autossh -M 20000 -fNT -L 8080:192.168.1.100:80 user@pivot.com

# Monitor port 20000 for connection health
# Automatically reconnects if connection drops
```

---

## **Troubleshooting SSH Tunnels**

### **Common Issues**

**1. Permission Denied**
[!WARNING] Ensure proper key permissions and test basic SSH connectivity.
```bash
# Check SSH key permissions
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub
chmod 700 ~/.ssh/

# Test SSH connection first
ssh -v user@pivot.com
```

**2. Port Already in Use**
[!WARNING] Identify and resolve port conflicts.
```bash
# Check what's using the port
netstat -tlnp | grep :8080
lsof -i :8080

# Kill process or use different port
ssh -L 8081:192.168.1.100:80 user@pivot.com
```

**3. Connection Refused**
[!WARNING] Ensure service is running and accessible on the target.
```bash
# Test from SSH server first
ssh user@pivot.com
curl http://192.168.1.100:80

# Check if service is running on target
nmap -p 80 192.168.1.100
```

**4. GatewayPorts Issue**
[!WARNING] Allow external connections to forwarded ports.
```bash
# Use `-g` option for external access
ssh -g -L 0.0.0.0:8080:192.168.1.100:80 user@pivot.com

# Or set in SSH server config (/etc/ssh/sshd_config)
GatewayPorts yes
```

### **Debugging Commands**
[!INFO] Use verbose and monitoring commands for troubleshooting.
```bash
# Verbose SSH output
ssh -v -L 8080:192.168.1.100:80 user@pivot.com

# Check tunnel status
netstat -tlnp | grep :8080
ss -tlnp | grep :8080

# Test tunnel connectivity
curl -v http://localhost:8080
nc -v localhost 8080
```

---

## **SSH Tunneling in Different Scenarios**

### **Scenario 1: Web Application Testing**
[!EXAMPLE] Set up a tunnel for testing web applications.
```bash
# Forward HTTP traffic to internal web app
ssh -fNT -L 8080:internal-web.corp.com:80 user@jumpbox.corp.com

# Use Burp Suite proxy for full control over requests
ssh -fNT -L 8080:internal-web.corp.com:80 -L 8443:internal-web.corp.com:443 user@jumpbox.corp.com

# Access through browser
firefox http://localhost:8080
```

### **Scenario 2: Database Access**
[!EXAMPLE] Securely access internal databases.
```bash
# Forward SQL Server traffic for remote database access
ssh -fNT -L 1433:sql.internal.corp:1433 user@jumpbox.corp.com

# Connect with sqlcmd or similar tool
sqlcmd -S localhost,1433 -U sa -P password

# Forward MySQL traffic and connect using forwarded port
ssh -fNT -L 3306:mysql.internal.corp:3306 user@jumpbox.corp.com
mysql -h 127.0.0.1 -P 3306 -u root -p
```

### **Scenario 3: Remote Desktop/VNC Access**
[!EXAMPLE] Set up RDP and VNC access to internal machines.
```bash
# Forward RDP traffic for remote desktop access
ssh -L 3389:windows.internal.corp:3389 user@jumpbox.corp.com

# Connect with rdesktop or similar tool
rdesktop localhost:3389

# Forward VNC port and connect using a VNC client
ssh -L 5900:linux.internal.corp:5900 user@jumpbox.corp.com
vncviewer localhost:5900
```

---

## **Advanced SSH Tunneling**

### **Multiple Hops (ProxyJump)**
[!INFO] Access final target through multiple hops.
```bash
# Use proxy jump to access final target
ssh -J user1@hop1.com,user2@hop2.com user3@final-target.com

# Forward port through multiple hops
ssh -J user@pivot1.com -L 8080:internal.local:80 user@pivot2.com
```

### **SSH Config File**
[!INFO] Configure `~/.ssh/config` for easier tunnel setup.
```bash
# ~/.ssh/config file example
Host pivot
    HostName 10.10.10.50
    User pentester
    Port 22
    LocalForward 8080 192.168.1.100:80
    LocalForward 3389 192.168.1.50:3389
    DynamicForward 1080

# Usage
ssh pivot
```

### **Persistent Tunnels with autossh**
[!INFO] Use `autossh` for persistent SSH tunnels.
```bash
# Install autossh
apt install autossh

# Persistent tunnel that automatically reconnects
autossh -M 20000 -fNT -L 8080:192.168.1.100:80 user@pivot.com

# Monitor port for connection health and auto-reconnect
```

---

## **Conclusion**
SSH tunneling is a powerful tool for securely accessing internal services over an encrypted link, providing flexibility in network configurations. This guide covers the basics to advanced use cases, ensuring you can effectively manage your SSH tunnels.

---

[!INFO] For further details and troubleshooting tips, refer to the official OpenSSH documentation or community forums. Happy tunneling!

--- 

## Appendix

### **Common Commands**
```bash
# List open connections
netstat -tlnp | grep ssh
lsof -i :8080

# Test connectivity with curl
curl http://localhost:8080

# Check SSH key permissions
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub
```


### **SSH Config File Example**
```bash
Host pivot
    HostName 10.10.10.50
    User pentester
    Port 22
    LocalForward 8080 192.168.1.100:80
    LocalForward 3389 192.168.1.50:3389
    DynamicForward 1080

Host jumpbox
    HostName 172.16.1.10
    User admin
    Port 22
```

### **Troubleshooting Tips**
- Ensure SSH keys are properly set up and permissions are correct.
- Check firewall rules on both local and remote machines.
- Use `-v` flag with `ssh` for detailed debugging information.

---

# 🛰️ End of Guide

For any questions or further assistance, feel free to reach out in the comments section below! 🚀🚀🚀

---


---
**You:**
Could you clarify how I can use autossh to maintain persistent SSH tunnels and monitor their health? Can you provide an example configuration?

---

[!INFO] `autossh` is a utility designed to automatically restart the ssh client if it dies or stops sending/receiving data. This ensures that your tunnel stays up even when network conditions are unreliable.

### **Using autossh for Persistent Tunnels**

#### Example Configuration

To maintain persistent SSH tunnels, you need to set up `autossh` correctly. Here's an example configuration:

```bash
# Install autossh if not already installed
sudo apt-get install autossh

# Configure autossh with the following command:
autossh -M 20000 -fN -L 8080:192.168.1.100:80 user@pivot.com
```

- `-M 20000`: Specifies a monitoring port (e.g., 20000) for autossh to listen on.
- `-fN`: Backgrounds the process after startup and prevents forwarding of signals from the terminal.
- `-L 8080:192.168.1.100:80`: Sets up a local port forward as usual.

#### Monitoring Configuration

The monitoring port (`-M 20000` in this case) allows `autossh` to periodically check the SSH connection's health. If it detects that the connection has dropped or is not sending/receiving data, it will automatically restart the tunnel.

You can monitor the status of your tunnels by checking connections on the monitoring port:

```bash
# Check if autossh is listening on the monitoring port
netstat -tlnp | grep 20000

# Alternatively, use lsof to find processes using that port
lsof -i :20000
```

#### Example SSH Config File

You can also configure `autossh` via your `~/.ssh/config` file:

```bash
Host pivot
    HostName 10.10.10.50
    User pentester
    Port 22
    LocalForward 8080 192.168.1.100:80
    DynamicForward 1080
    RemoteCommand autossh -M 20000 -fN -L 8080:192.168.1.100:80 user@pivot.com
```

This configuration ensures that `autossh` is launched automatically when you connect to the host specified in your SSH config.

---

By setting up `autossh`, you can ensure that your tunnels stay up even under less-than-ideal network conditions, providing a robust and reliable solution for maintaining persistent connections. 🚀🚀🚀

---


# 🛰️ Additional Tips
[!INFO] Here are some additional tips to further enhance your SSH tunneling experience:

### **Tips**

1. **Key Management**: Regularly rotate your SSH keys and ensure they have proper permissions (600) to prevent unauthorized access.
2. **Firewall Rules**: Ensure that firewall rules on both the local and remote machines allow necessary traffic for SSH, forwarded ports, and monitoring ports.
3. **Logging**: Enable logging in `/etc/ssh/sshd_config` with `LogLevel VERBOSE` or `LogLevel DEBUG` to capture more detailed information about tunnel connections.
4. **Security Practices**: Use strong passwords or public key authentication for increased security. Consider using tools like `fail2ban` to protect against brute-force attacks.
5. **Monitoring Tools**: Utilize monitoring tools such as Nagios, Zabbix, or even simple scripts to alert you when tunnels go down.

By following these tips and the steps outlined in this guide, you can establish a reliable and secure SSH tunneling environment for various network configurations and use cases. 🚀🚀🚀

---


# 🛰️ End of Guide

Feel free to reach out with any additional questions or if you need further assistance! 🚀🚀🚀