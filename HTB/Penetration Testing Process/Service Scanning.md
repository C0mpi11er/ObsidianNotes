
### 📌 What is a Service?

- A **service** = application running on a system (e.g., FTP, SSH, HTTP)
- Systems hosting services = **servers**
- Goal: find **misconfigurations or vulnerabilities** to exploit

---

## 🌐 Networking Basics

- Each machine has an **IP address**
- Services run on **ports (1–65535)**
    - **1–1023** → well-known (privileged)
    - **1024+** → user services
- To interact: need **IP + port + protocol**

---

## 🔍 Nmap (Main Scanning Tool)

### Basic Scan

nmap <target>

- Scans **top 1000 ports**
- Shows:
    - Open ports
    - Service names

### Full + Detailed Scan

nmap -sC -sV -p- <target>

- `-sC` → default scripts (extra info)
- `-sV` → service/version detection
- `-p-` → scan all ports

### Key Output Fields

- **PORT** → port number
- **STATE** → open / filtered / closed
- **SERVICE** → expected service
- **VERSION** → actual software + version

---

## 🧬 OS & Version Fingerprinting

- Done via:
    - Service versions (e.g., OpenSSH)
    - Nmap scripts
- ⚠️ Not always reliable (custom installs possible)

---

## 🧰 Nmap Scripts

- Extend functionality (vuln scanning, enumeration)

nmap --script <script> -p<port> <target>

---

## 🎯 Banner Grabbing

- Extract service info when connecting

### Example:

nc -nv <target> 21

→ reveals service/version (e.g., vsFTPd 3.0.3)

---

## 📂 FTP Enumeration (Port 21)

- Check for:
    - **Anonymous login**
    - Accessible files

### Basic Workflow

ftp <target>

Commands:

- `ls` → list files
- `cd` → change dir
- `get` → download file

📌 Common finding:

- Credentials in files (e.g., `login.txt`)

---

## 🪟 SMB Enumeration (Port 445)

### What is SMB?

- File sharing protocol (Windows/Linux via Samba)
- High-value target (credentials, RCE)

### OS Discovery

nmap --script smb-os-discovery.nse -p445 <target>

### List Shares

smbclient -N -L //<target>

### Access Share

smbclient //<target>/users -U <user>

📌 Look for:

- ملفات (files) with passwords
- Misconfigured access

---

## 📡 SNMP Enumeration (Port 161)

### Key Idea

- Uses **community strings** like passwords:
    - Default: `public`, `private`
- SNMP v1/v2c = **no encryption**
- SNMP v3 = secure

### Example

snmpwalk -v 2c -c public <target>

### Brute Force Community Strings

onesixtyone -c dict.txt <target>

📌 Can reveal:

- System info
- Running processes
- Credentials
- Network data

---

## 🔥 Key Takeaways

- Enumeration = **most important phase**
- Always:
    - Scan → Identify services → Enumerate deeply
- Focus on:
    - Misconfigs (anonymous FTP, open SMB)
    - Versions (vulnerabilities)
    - Credentials leakage

---


command summary-------------------------------------------------------------------------------------- 

|**Service Scanning**||
|`nmap 10.129.42.253`|Run nmap on an IP|
|`nmap -sV -sC -p- 10.129.42.253`|Run an nmap script scan on an IP|

|`locate scripts/citrix`|List various available nmap scripts|

|`nmap --script smb-os-discovery.nse -p445 10.10.10.40`|Run an nmap script on an IP|

|`netcat 10.10.10.10 22`|Grab banner of an open port|

|`smbclient -N -L \\\\10.129.42.253`|List SMB Shares|

|`smbclient \\\\10.129.42.253\\users`|Connect to an SMB share|

 smbclient -U bob \\\\10.129.47.195\\users | connect to user bob


|`snmpwalk -v 2c -c public 10.129.42.253 1.3.6.1.2.1.1.5.0`|Scan SNMP on an IP|

|`onesixtyone -c dict.txt 10.129.42.254`|Brute force SNMP secret string|


>#[!TIP]!!!! very vital command to help find vuln
>searchsploit   --nmap  nmap-scan.xml
