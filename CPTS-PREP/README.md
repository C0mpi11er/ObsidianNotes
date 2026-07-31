# 🛰️ Comprehensive Penetration Testing Preparation Guide

---

## **Enumeration & Scanning**

### 🔍 Network Services Enumeration
- **[[TCP and UDP Ports Scan]]** - TCP/UDP port identification and service discovery using Nmap
- **[[Service Version Detection]]** - Detailed version enumeration of running services on a target system

### 🕸️ Web Information Gathering
- **[Web Information Gathering](./web-enumeration/web-information-gathering.md)** - Overview and quick start guide for web reconnaissance
- **[Subdomain Enumeration](./web-enumeration/subdomain-enumeration.md)** - DNS enumeration and subdomain discovery techniques
- **[Web Application Enumeration](./web-enumeration/web-application-enumeration.md)** - Directory enumeration, virtual hosts, and web application testing

### 🔎 Infrastructure & Network Discovery
- **[[Footprinting]]** - Domain information gathering, cloud service identification, certificate transparency analysis, subdomain discovery
- **[[Firewall Evasion]]** - Techniques for bypassing security controls such as IDS/IPS evasion methods and protocol manipulation

---

## **Exploitation**

### ⚡ Privilege Escalation Techniques

#### Windows Privilege Escalation
- **[[Windows Privilege Escalation Overview]]**
  - **[Initial Enumeration](./windows-privilege-escalation/windows-privilege-esc/initial-enumeration.md)**
    - System and user environment assessment
    - Permissions and group membership checks
  - **[Privilege Exploitation](./windows-privilege-escalation/windows-privilege-esc/seimperatedebug.md)**
    - SeImpersonate, SeDebugPrivilege, SeTakeOwnershipPrivilege exploitation
  - **[Built-in Groups Abuse](./windows-privilege-escalation/windows-privilege-esc/built-in-groups-abuse.md)**
    - Backup Operators, Event Log Readers, DnsAdmins, Hyper-V Administrators, Print Operators, Server Operators misuse
  - **[UAC Bypass Techniques](./windows-privilege-escalation/windows-privilege-esc/uac-bypass.md)**
    - Common UAC bypass methods and automated tools usage
  - **[Weak Permissions Exploitation](./windows-privilege-escalation/windows-privilege-esc/weak-permissions-exploit.md)**
    - Misconfigured file permissions and service misconfigurations exploitation
  - **[Kernel Exploits](./windows-privilege-escalation/windows-privilege-esc/kernel-exploits.md)**
    - HiveNightmare, PrintNightmare, legacy kernel vulnerabilities
  - **[Third-party Service Vulnerabilities](./windows-privilege-escalation/windows-privilege-esc/third-party-vulnerabilities.md)**
    - Exploiting vulnerable third-party services on Windows systems
  - **[Credential Hunting Techniques](./windows-privilege-escalation/windows-privilege-esc/credential-hunting.md)**
    - Browsing for credentials in browsers, password managers, and automated tools
  - **[Advanced File System Searches](./windows-privilege-escalation/windows-privilege-esc/file-system-searches.md)**
    - Advanced file system enumeration techniques
  - **[Systematic Escalation Techniques](./windows-privilege-escalation/windows-privilege-esc/systematic-escalation.md)**
    - Systematic escalation methods and automation

#### Linux Privilege Escalation
- **[[Linux Hardening]]** - Defensive security measures and system hardening
  - **Update Management**
    - Kernel and package update strategies for vulnerability mitigation
  - **Configuration Hardening**
    - File system, service, and user management security practices
- **[Polkit/Pwnkit](./linux-priv-esc/polkit-pwnkit.md)** - Universal privilege escalation via polkit vulnerability
  - **CVE-2021-4034 Pwnkit Exploitation** - Memory corruption in pkexec for universal root access
  - **Zero-Prerequisite Escalation** - Any local user exploitation without authentication
- **[Dirty Pipe](./linux-priv-esc/dirty-pipe.md)** - Kernel vulnerability exploitation for file modification
  - **CVE-2022-0847 Kernel Exploitation** - Pipe mechanism abuse for arbitrary root file writes
  - **File Modification Attacks** - /etc/passwd modification and SUID binary hijacking via kernel exploit

---

## **Web Application Attacks**

### XSS (Cross-Site Scripting)
- **[XSS-Cross-Site-Scripting.md]**
  - Stored, Reflected, and DOM-based XSS with HTB Academy techniques

### File Inclusion
- **[[File Inclusion]]** 
  - Comprehensive LFI/RFI module with 9 specialized guides covering Basic Techniques, Advanced Bypasses, PHP Wrappers RCE, Remote File Inclusion, File Upload + LFI, Log Poisoning, Automated Scanning, Prevention & Hardening, and complete HTB Academy Skills Assessment

### File Upload Attacks
- **[[File Upload Attacks]]**
  - Complete file upload exploitation guide covering web shells, reverse shells, bypass techniques, and HTB Academy lab solutions

### Command Injection Attacks
- **[[Command Injection Attacks]]**
  - Comprehensive module with 10 sections: Detection + Exploitation + Filter Bypasses + Advanced Obfuscation + Skills Assessment
  - OS Command Execution with direct and blind injection techniques, filter bypass methods, advanced evasion and automated tools, complete methodology with HTB Academy lab solutions

### Web Attacks
- **[[HTTP Verb Tampering]]**
  - Methodology for detecting and exploiting HTTP verb tampering vulnerabilities
- **[[IDOR (Insecure Direct Object References)]]**
  - Overview of IDOR vulnerability and exploitation techniques
- **[[XXE (XML External Entities)]]**
  - Detection, exploitation, and prevention of XXE attacks

---

## **Password Attacks & Lateral Movement**

### Skills Assessment Workflow
- **[Skills Assessment Workflow](./passwords-attacks/skills-assessment-workflow.md)** 
  - Complete password attacks methodology from foothold to domain compromise

### Pass the Hash Attacks
- **[[Pass the Hash Attacks]]**
  - NTLM hash relay and authentication bypass techniques

### Pass the Ticket Attacks
- **[[Pass the Ticket Attacks]]**
  - Kerberos ticket manipulation and Golden Ticket attacks

### Pass the Certificate Attacks
- **[[Pass the Certificate Attacks]]**
  - ESC8 ADCS attacks and PKINIT exploitation

### NTDS.dit Attacks
- **[[NTDS.dit Attacks]]**
  - Domain controller credential extraction methods

---

## **Practical Application**

### Skills Assessment Walkthroughs
- **[Complete Skills Assessment](./pivoting-tunneling-port-forwarding/skills-assessment-complete-walkthrough.md)** 
  - All 7 HTB Academy questions with full solutions and troubleshooting
- **[Skills Assessment](./pivoting-tunneling-port-forwarding/skills-assessment.md)**
  - Hands-on lab scenarios and HTB Academy exercises

---

## **Key Features**

### Comprehensive Coverage
- **30+ Service Types** - Complete enumeration guides for all major services
- **Complete Attack Modules** 
  - Full HTB Academy "Attacking Common Services" (4,262 lines) + "Attacking Common Applications" (22 documents)
- **Web Application Attacks**
  - XSS (Cross-Site Scripting), File Inclusion module (9 specialized guides), File Upload Attacks (9 comprehensive sections), Command Injection (10 comprehensive sections), and Web Attacks (HTTP Verb Tampering, IDOR, XXE)

### Practical Focus
- **Step-by-step Commands** 
  - Copy-paste ready enumeration commands
- **Tool Comparisons**
  - Multiple tools for each enumeration task
- **Security Assessment**
  - Vulnerability identification and exploitation
- **Defensive Measures**
  - Hardening and protection recommendations

---

## **Study Resources**

### Essential Reading
- **HTB Academy CPTS Path** 
  - Official certification curriculum
- **PTES Standard**
  - Penetration Testing Execution Standard
- **NIST Guidelines**
  - Cybersecurity framework references
- **OWASP Top 10**
  - Web application security fundamentals

### Required Tools
- **[[Nmap]]**
  - Network discovery and security auditing
- **[[Burp Suite]]** 
  - Web application security testing
- **[[Metasploit]]** 
  - Penetration testing framework
- **[[Bloodhound]]** 
  - Active Directory environment analysis
- **Custom Scripts** 
  - Automation and efficiency tools

### Certification Path
1. **Study Phase**
   - Review all enumeration guides systematically
2. **Lab Practice**
   - Complete HTB Academy lab exercises
3. **Exam Preparation**
   - Review methodologies and checklists
4. **Certification Exam**
   - Apply knowledge in simulated environment