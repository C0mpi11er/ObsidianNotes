# 🛰️ Introduction

## 🔍 Overview
[!INFO] This guide covers advanced usage of the `netsh` command in Windows for port forwarding and pivoting scenarios, including stealth techniques, integration with other tools, and best practices. It provides detailed examples to demonstrate how to create persistent and ephemeral port forwards using native utilities.

---

# 🎯 Usage Scenarios

## 🔍 Basic Port Forwarding
[!CHECK] Create a basic port forward from the attacker's machine to a target service running on another internal network.
```cmd
netsh.exe interface portproxy add v4tov4 listenport=8080 listenaddress=127.0.0.1 connectport=3389 connectaddress=172.16.5.19
```

## 🔍 Persistent Port Forwarding
[!CHECK] Set up a persistent port forward to maintain access after reboot.
```cmd
netsh.exe interface portproxy add v4tov4 listenport=8080 listenaddress=10.129.15.150 connectport=3389 connectaddress=172.16.5.19 persistent=true
```

## 🔍 Ephemeral Port Forwarding
[!WARNING] Create a temporary port forward that does not persist after reboot.
```cmd
netsh.exe interface portproxy add v4tov4 listenport=8080 listenaddress=127.0.0.1 connectport=3389 connectaddress=172.16.5.19 persistent=false
```

## 🔍 Stealth Port Forwarding
[!INFO] Use a non-standard port to avoid common detection mechanisms.
```cmd
netsh.exe interface portproxy add v4tov4 listenport=8081 listenaddress=127.0.0.1 connectport=3389 connectaddress=172.16.5.19 persistent=false
```

---

# 🛠️ Advanced Techniques

## 🔍 Multiple Port Forwarding Rules
[!CHECK] Set up multiple port forwards for different services.
```cmd
netsh.exe interface portproxy add v4tov4 listenport=8082 listenaddress=127.0.0.1 connectport=80 connectaddress=192.168.15.10
netsh.exe interface portproxy add v4tov4 listenport=8083 listenaddress=127.0.0.1 connectport=445 connectaddress=192.168.15.10
```

## 🔍 Port Forwarding for HTTP Services
[!SUCCESS] Redirect incoming HTTP traffic to a web server on the internal network.
```cmd
netsh.exe interface portproxy add v4tov4 listenport=80 listenaddress=192.168.15.10 connectport=8080 connectaddress=172.16.5.19 persistent=true
```

## 🔍 HTTPS Forwarding
[!SUCCESS] Redirect incoming HTTPS traffic to an internal service.
```cmd
netsh.exe interface portproxy add v4tov4 listenport=443 listenaddress=10.129.15.150 connectport=8443 connectaddress=172.16.5.19 persistent=true
```

## 🔍 Dynamic Forwarding with PowerShell
[!INFO] Use PowerShell to automate the creation of port forwarding rules.
```powershell
function New-PortForward {
    param(
        [int]$ListenPort,
        [string]$ListenAddress,
        [int]$ConnectPort,
        [string]$ConnectAddress
    )
    
    $cmd = "netsh.exe interface portproxy add v4tov4 listenport=$ListenPort listenaddress=$ListenAddress connectport=$ConnectPort connectaddress=$ConnectAddress"
    Invoke-Expression $cmd
}

New-PortForward -ListenPort 8081 -ListenAddress "192.168.15.10" -ConnectPort 3389 -ConnectAddress "172.16.5.19" -Persistent true
```

## 🔍 Forwarding to a Specific IP Address
[!SUCCESS] Specify a particular IP address as the target for port forwarding.
```cmd
netsh.exe interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=3389 connectaddress=172.16.5.19 persistent=true
```

## 🔍 Forwarding to a Specific IP Address and Port
[!SUCCESS] Use the `connectaddress` parameter to forward traffic to a specific internal address.
```cmd
netsh.exe interface portproxy add v4tov4 listenport=8080 listenaddress=127.0.0.1 connectport=3389 connectaddress=172.16.5.19 persistent=true
```

## 🔍 Forwarding to Multiple Internal IPs
[!SUCCESS] Set up port forwarding rules for multiple internal IP addresses.
```cmd
netsh.exe interface portproxy add v4tov4 listenport=8080 listenaddress=127.0.0.1 connectport=3389 connectaddress=172.16.5.19 persistent=true
netsh.exe interface portproxy add v4tov4 listenport=8081 listenaddress=127.0.0.1 connectport=3389 connectaddress=172.16.5.20 persistent=true
```

## 🔍 Forwarding to an Internal Network Range
[!SUCCESS] Create a port forward for a range of internal IP addresses.
```cmd
netsh.exe interface portproxy add v4tov4 listenport=8080 listenaddress=192.168.15.0 connectport=3389 connectaddress=172.16.5.0 persistent=true
```

## 🔍 Forwarding to an Internal Network and Port Range
[!SUCCESS] Set up port forwarding for a range of internal IP addresses and ports.
```cmd
netsh.exe interface portproxy add v4tov4 listenport=8080 listenaddress=192.168.15.0 connectport=3389-3390 connectaddress=172.16.5.0 persistent=true
```

## 🔍 Forwarding to an Internal Network and Specific Ports
[!SUCCESS] Use `connectaddress` parameter to forward traffic for specific ports.
```cmd
netsh.exe interface portproxy add v4tov4 listenport=8080 listenaddress=127.0.0.1 connectport=3389,445 connectaddress=172.16.5.19 persistent=true
```

## 🔍 Forwarding to a Specific IP Address and Port Range
[!SUCCESS] Set up port forwarding for a specific internal address with multiple ports.
```cmd
netsh.exe interface portproxy add v4tov4 listenport=8080 listenaddress=127.0.0.1 connectport=3389-3390 connectaddress=172.16.5.19 persistent=true
```

## 🔍 Forwarding to a Specific IP Address and Port with Persistence
[!SUCCESS] Ensure the port forwarding rule is persistent across reboots.
```cmd
netsh.exe interface portproxy add v4tov4 listenport=8080 listenaddress=127.0.0.1 connectport=3389 connectaddress=172.16.5.19 persistent=true
```

---

# 📜 Common Issues and Solutions

## 🔍 Invalid Port Forwarding Rules
[!WARNING] Correct common errors in port forwarding rules.
```cmd
netsh.exe interface portproxy delete v4tov4 listenport=8080 listenaddress=127.0.0.1
```

## 🔍 IP Address Resolution Issues
[!INFO] Use nslookup or similar tools to resolve DNS issues.
```bash
nslookup 172.16.5.19
```

## 🔍 Network Connectivity Problems
[!WARNING] Verify network connectivity with ping or tracert commands.
```cmd
ping -n 4 172.16.5.19
tracert 172.16.5.19
```

---

# 🛠️ Stealth Techniques

## 🔍 Non-Standard Port Forwarding
[!SUCCESS] Redirect traffic on non-standard ports.
```cmd
netsh.exe interface portproxy add v4tov4 listenport=8083 listenaddress=127.0.0.1 connectport=8082 connectaddress=192.168.15.10 persistent=true
```

## 🔍 DNS Spoofing with Netsh
[!WARNING] Use netsh to create a local DNS entry.
```cmd
netsh.exe dns add server=127.0.0.1 address=example.com 192.168.15.10
```

## 🔍 IP Spoofing with Netsh
[!DANGER] Use netsh to create a local IP mapping.
```cmd
netsh.exe interface ipv4 add neighbors "Local Area Connection" 172.16.5.19 192.168.15.10
```

## 🔍 DNS Hijacking with Netsh
[!DANGER] Use netsh to hijack DNS responses.
```cmd
netsh.exe interface ipv4 add dnsservers "Local Area Connection" address=192.168.15.10 index=1
```

---

# 🛠️ Troubleshooting

## 🔍 Checking Existing Rules
[!INFO] List all existing port forwarding rules.
```cmd
netsh.exe interface portproxy show all
```

## 🔍 Deleting Port Forwarding Rules
[!SUCCESS] Remove a specific rule by its parameters.
```cmd
netsh.exe interface portproxy delete v4tov4 listenport=8080 listenaddress=127.0.0.1
```

## 🔍 Resolving Connectivity Issues
[!INFO] Use `ping` and `tracert` to check connectivity.
```cmd
ping -n 4 172.16.5.19
tracert 172.16.5.19
```

---

# 🛠️ Integration with Other Tools

## 🔍 Integrating with PowerShell for Automation
[!INFO] Create a script to automate port forwarding.
```powershell
function New-PortForward {
    param(
        [int]$ListenPort,
        [string]$ListenAddress,
        [int]$ConnectPort,
        [string]$ConnectAddress
    )
    
    $cmd = "netsh.exe interface portproxy add v4tov4 listenport=$ListenPort listenaddress=$ListenAddress connectport=$ConnectPort connectaddress=$ConnectAddress"
    Invoke-Expression $cmd
}

New-PortForward -ListenPort 8081 -ListenAddress "192.168.15.10" -ConnectPort 3389 -ConnectAddress "172.16.5.19" -Persistent true
```

## 🔍 Integration with Nmap for Port Scanning
[!INFO] Use `nmap` to scan ports before setting up forwarding.
```bash
nmap -p 3389 172.16.5.19
```

## 🔍 Integration with Wireshark for Traffic Analysis
[!INFO] Use `Wireshark` to monitor traffic on the forwarded port.
```cmd
wireshark -i "Local Area Connection"
```

---

# 📄 Persistent Port Forwarding

## 🔍 Creating Persistent Rules
[!SUCCESS] Set up persistent rules with netsh.
```cmd
netsh.exe interface portproxy add v4tov4 listenport=8080 listenaddress=127.0.0.1 connectport=3389 connectaddress=172.16.5.19 persistent=true
```

## 🔍 Removing Persistent Rules
[!SUCCESS] Delete persistent rules to avoid reboots.
```cmd
netsh.exe interface portproxy delete v4tov4 listenport=8080 listenaddress=127.0.0.1
```

---

# 📄 Best Practices and Recommendations

## 🔍 Using Non-Standard Ports for Stealth
[!INFO] Use non-standard ports to avoid detection.
```cmd
netsh.exe interface portproxy add v4tov4 listenport=8083 listenaddress=127.0.0.1 connectport=3389 connectaddress=172.16.5.19 persistent=true
```

## 🔍 Securing Port Forwarding Rules
[!SUCCESS] Implement strict firewall rules and monitoring.
```cmd
netsh.exe advfirewall firewall add rule name="PortForwardingRule" dir=in action=allow protocol=tcp localport=8083 remoteip=172.16.5.19
```

## 🔍 Monitoring Port Forwarding Rules
[!INFO] Use `netstat` to monitor active port forwarding rules.
```cmd
netstat -an | findstr :8083
```

---

# 📜 Removing Persistent Rules

## 🔍 Deleting Persistent Rules Manually
[!SUCCESS] Remove a persistent rule by its parameters.
```cmd
netsh.exe interface portproxy delete v4tov4 listenport=8080 listenaddress=127.0.0.1
```

## 🔍 Removing All Persistent Rules
[!WARNING] Delete all persistent rules in one go.
```cmd
netsh.exe interface portproxy reset
```

---

# 🛠️ Integration with Other Tools

## 🔍 Integrating with Nmap for Port Scanning
[!INFO] Use `nmap` to scan ports before setting up forwarding.
```bash
nmap -p 3389 172.16.5.19
```

## 🔍 Integrating with Wireshark for Traffic Analysis
[!INFO] Use `Wireshark` to monitor traffic on the forwarded port.
```cmd
wireshark -i "Local Area Connection"
```

---

# 📄 Removing Persistent Rules

## 🔍 Deleting a Single Rule
[!SUCCESS] Remove a single persistent rule by its parameters.
```cmd
netsh.exe interface portproxy delete v4tov4 listenport=8080 listenaddress=127.0.0.1
```

## 🔍 Removing All Rules
[!WARNING] Reset all rules to their default state.
```cmd
netsh.exe interface portproxy reset
```

---

# 📄 Monitoring and Logging

## 🔍 Using `netstat` for Real-Time Monitoring
[!INFO] Use `netstat` to monitor active connections.
```cmd
netstat -an | findstr :8083
```

## 🔍 Using Event Viewer for Log Analysis
[!INFO] Check the Windows Event Viewer for port forwarding events.
```powershell
Get-WinEvent -FilterHashtable @{LogName='System'; ID=4697}
```

---

# 📄 Persistence Techniques

## 🔍 Creating Persistent Rules with Group Policy
[!SUCCESS] Set up persistent rules using Group Policy.
```cmd
netsh.exe interface portproxy add v4tov4 listenport=8083 listenaddress=127.0.0.1 connectport=3389 connectaddress=172.16.5.19 persistent=true
```

## 🔍 Using Registry Keys for Persistence
[!SUCCESS] Add registry keys to maintain persistence.
```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Services\Tcpip6\Parameters" /v PortProxy /t REG_SZ /d "listenport=8083 listenaddress=127.0.0.1 connectport=3389 connectaddress=172.16.5.19"
```

---

# 📄 Backup and Restore

## 🔍 Backing Up Netsh Rules
[!SUCCESS] Save all netsh rules to a file.
```cmd
netsh.exe interface portproxy dump > c:\netshbackup.txt
```

## 🔍 Restoring Netsh Rules from Backup
[!SUCCESS] Restore netsh rules from a backup file.
```cmd
netsh.exe exec c:\netshbackup.txt
```

---

# 📄 Additional Commands

## 🔍 Listing All Port Forwarding Rules
[!INFO] List all existing port forwarding rules.
```cmd
netsh.exe interface portproxy show all
```

## 🔍 Deleting a Specific Rule
[!SUCCESS] Remove a specific rule by its parameters.
```cmd
netsh.exe interface portproxy delete v4tov4 listenport=8083 listenaddress=127.0.0.1
```

---

# 📄 Documentation and Resources

## 🔍 Official Microsoft Documentation
[!INFO] Refer to the official documentation for detailed information.
```url
https://docs.microsoft.com/en-us/windows-server/networking/technologies/netsh/netsh-portproxy
```

## 🔍 Community Forums and Blogs
[!INFO] Visit community forums and blogs for additional insights and examples.
```url
https://www.reddit.com/r/sysadmin/
https://pentestlab.github.io/
```

---

# 📄 Conclusion

This guide provides a comprehensive overview of advanced `netsh` port forwarding techniques, including stealth methods, integration with other tools, and best practices. By following these instructions, you can effectively set up persistent and ephemeral port forwards to maintain access in various scenarios.

---


## 🔍 References
[!INFO] For more information and detailed documentation on `netsh`, refer to the official Microsoft documentation and community resources.
```url
https://docs.microsoft.com/en-us/windows-server/networking/technologies/netsh/netsh-portproxy
```

# 📄 License

This guide is provided under a Creative Commons Attribution-NonCommercial-NoDerivatives 4.0 International (CC BY-NC-ND 4.0) license.

---

# 🛠️ Contributing

Contributions are welcome! Please submit pull requests or contact the author for further details.
```email
author@example.com
```

---


## 🔍 Acknowledgements
[!INFO] Special thanks to contributors and community members who have provided valuable insights and feedback on this guide.

---

# 📄 Disclaimer

This guide is intended for educational purposes only. Use any information or techniques at your own risk, following all relevant laws and regulations. The author and contributors are not responsible for misuse of the content in this document.
```url
https://www.gnu.org/licenses/gpl-3.0.en.html
```