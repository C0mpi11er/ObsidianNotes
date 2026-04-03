# 🌐 Web Enumeration

## 📌 Overview

- Web servers commonly run on:
    - **Port 80 (HTTP)**
    - **Port 443 (HTTPS)**
- Web apps = **high-value targets**
- Goal: discover **hidden functionality, misconfigs, and vulnerabilities**

---

## 📂 Directory & File Enumeration

### 🔧 Tools

- **Gobuster**
- **ffuf**

### Basic Command

gobuster dir -u http://<target>/ -w <wordlist>

### 📊 Important Status Codes

- **200** → OK (exists)
- **301/302** → Redirect (useful)
- **403** → Forbidden (exists but blocked)

### 🎯 What to Look For

- Hidden directories (`/admin`, `/backup`)
- Sensitive files (`.htpasswd`, configs)
- Web apps (e.g., `/wordpress`)

📌 Example Finding:

- WordPress setup page → possible **RCE**

---

## 🌍 Subdomain Enumeration

### 🔧 Tool

gobuster dns -d <domain> -w <wordlist>

### 📌 Purpose

- Find:
    - Admin panels
    - APIs
    - Internal apps

### 🎯 Example Findings

- `admin.domain.com`
- `blog.domain.com`
- `customer.domain.com`

---

## 🧾 Banner Grabbing (Web)

### 🔧 Tool

curl -IL <target>

### 📌 Extract:

- Web server (Apache, Nginx)
- Frameworks
- Headers

📌 Example:

- `Apache/2.4.29 (Ubuntu)`

---

## 🔍 Technology Fingerprinting

### 🔧 Tool: whatweb

whatweb <target>

### 📌 Reveals:

- Server type
- Frameworks (PHP, jQuery)
- Versions

🎯 Helps:

- Identify **vulnerable software**

---

## 🔐 SSL/TLS Certificates

### 📌 Check for:

- Company name
- Email addresses
- Domains/subdomains

🎯 Use Cases:

- Recon
- Phishing (if in scope)

---

## 🤖 robots.txt

### 📌 Location

http://<target>/robots.txt

### 📌 Purpose

- Tells search engines what to ignore

### 🎯 Value

- Often exposes:
    - `/admin`
    - `/private`
    - `/uploads`

---

## 💻 Source Code Review

### 🔧 Shortcut

- `CTRL + U` (view page source)

### 🎯 Look For:

- Comments
- Hidden endpoints
- Hardcoded credentials

📌 Example:

<!-- test account: admin:test123 -->

---

## 🧠 Key Takeaways

- Web enumeration = **critical attack surface discovery**
- Always check:
    - Directories
    - Subdomains
    - Headers
    - Source code
- Focus on:
    - Hidden functionality
    - Misconfigurations
    - Information leakage

---

## ⚡ Practical Workflow

1. Visit site manually
2. Run **Gobuster (dirs)**
3. Run **Gobuster (DNS)**
4. Check **headers (curl)**
5. Run **whatweb**
6. Check:
    - `robots.txt`
    - Source code
    - SSL cert



<script>



|   |   |
|---|---|
|`gobuster dir -u http://10.10.10.121/ -w /usr/share/dirb/wordlists/common.txt`|Run a directory scan on a website|

|`gobuster dns -d inlanefreight.com -w /usr/share/SecLists/Discovery/DNS/namelist.txt`|Run a sub-domain scan on a website|

|`curl -IL https://www.inlanefreight.com`|Grab website banner|

|`whatweb 10.10.10.121`|List details about the webserver/certificates|

|`curl 10.10.10.121/robots.txt`|List potential directories in `robots.txt`|
|`ctrl+U`|View page source (in Firefox)|


</commands>
