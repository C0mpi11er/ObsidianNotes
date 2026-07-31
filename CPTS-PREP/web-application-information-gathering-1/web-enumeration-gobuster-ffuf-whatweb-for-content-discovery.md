```markdown
# 🌐 Web Enumeration - gobuster, ffuf, whatweb for content discovery

---

> [!ABSTRACT] Overview
> This section covers the use of `gobuster`, `ffuf`, and `whatweb` tools for discovering hidden directories, files, subdomains, and technologies in web applications. These techniques are crucial for initial reconnaissance and enumeration phases during a cybersecurity assessment.

## 📚 Tools Description

### gobuster

**Gobuster** is an HTTP/HTTPS/Gopher file/directory bruteforcing tool written in Go that allows you to discover hidden paths, directories, and files on web servers. It supports a variety of input types including lists, regex patterns, and more.

```text
gobuster dir -u http://target.com -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

### ffuf

**FFUF** is a fast URL Fuzzer written in Go that can be used to brute force URLs for directories, files, and subdomains on target systems. FFUF supports custom word lists and output formats.

```text
ffuf -u http://target.com/FUZZ -w /usr/share/seclists/Discovery/Web_Content/directory-list-2.3-medium.txt
```

### whatweb

**WhatWeb** is a tool that identifies technologies, tags, HTTP headers, JavaScript applications, and more from web sites, blogs, and wikis. It provides valuable information about the server technology stack.

```text
whatweb http://target.com
```

---

## 🛠️ Usage Examples

### Enumerating Directories with gobuster

```bash
gobuster dir -u http://example.com -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
```

This command will attempt to discover directories on `http://example.com` using a small wordlist.

### Enumerating Subdomains with ffuf

```bash
ffuf -u http://example.com/FUZZ -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -mc 200,403 -o subdomains.txt -of csv
```

This command uses `ffuf` to enumerate potential subdomains of `http://example.com`.

### Technology Detection with whatweb

```bash
whatweb http://example.com --verbose
```

Running this command will provide detailed information about the technologies used on `http://example.com`.

---

## 📝 Important Points

- Ensure you have permission before running these tools against any target.
- Use smaller wordlists for efficiency and to avoid detection by WAFs.

> [!WARNING]
> Be cautious when using these tools as they may trigger alerts or blocklist your IP if overused or misconfigured.

---

## 📄 Example Outputs

### gobuster Output Sample

```text
=====================================================
/ (Status: 301)
/css (Status: 200)
/images (Status: 403)
/js (Status: 403)
/server-status (Status: 403)
```

### ffuf Output Sample

```text
[+] URL: http://example.com/FUZZ
[+] Wordlist: subdomains-top1million-110000.txt (67289 words)
[+] Status codes to ignore (default): [400,403,404,405]
[+] Starting at URL: http://example.com/
[+] Resuming from previously crashed wordlist position 1
[+] Mode: HTML
[+] Number of URLs to scan per CPU: 1
[+] Starting new HTTP session
[+] Attempting to match regular expression: /<title>(.*)<\/title>/
[*] https://example.com:443/ (Status: 200 | Size: 7965)
```

### whatweb Output Sample

```text
http://example.com [200 OK]
Server: Apache/2.4.18 (Ubuntu) OpenSSL/1.0.2l PHP/5.6.30-0+deb.sury.org~xenial+1
X-Powered-By: PHP/5.6.30-0+deb.sury.org~xenial+1
Vary: Accept-Encoding, Cookie
Content-Type: text/html; charset=UTF-8
X-Frame-Options: SAMEORIGIN
Cache-Control: no-cache, must-revalidate, max-age=0
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Date: Mon, 30 Mar 2020 14:59:09 GMT
Connection: close
Content-Length: 7965
X-XSS-Protection: 1; mode=block
Last-Modified: Fri, 27 Mar 2020 18:59:37 GMT
ETag: "5e7f4a9d-1eef"
Accept-Ranges: bytes

MySQL (Database)
PHP (Web Application Framework)
```

---

## 🔍 Common Findings and Exploits

| Finding | Description |
|---|---|
| Hidden Admin Panels | Often protected by weak passwords or misconfigured access controls. |
| Unsecured APIs | May expose sensitive information or allow unauthorized actions without proper authentication. |
| Insecure Config Files | Leaked configuration files can reveal database credentials, API keys, and other secrets. |
| Outdated Software | Vulnerable software can be exploited to gain unauthorized access or execute arbitrary code. |

---

## ⚠️ Potential Risks

- **Data Leakage**: Discovering sensitive information that could be used maliciously.
- **Network Overload**: Aggressive scanning may overload network resources and cause service disruption.

> [!DANGER]
> Ensure you have explicit authorization before performing any actions that might impact the availability or security of a system.

---

## 🧠 Mental Model

When conducting web enumeration:
1. Identify directories, files, subdomains, and technologies.
2. Analyze outputs for vulnerabilities and misconfigurations.
3. Document findings for further exploitation or reporting purposes.

> [!SUCCESS]
> Successful enumeration provides critical insights into the target’s architecture and weak points that can be exploited later in a penetration test.

---
```