```markdown
# 🛰️ FTP - Port 21, anonymous access, file upload/download

> [!ABSTRACT] Overview of FTP on Port 21
>
> This section provides an overview of File Transfer Protocol (FTP) running on port 21 and discusses the implications of having anonymous access enabled. It outlines steps for enumerating and exploiting FTP services to gain unauthorized access or download sensitive files.

---

## 🔍 Initial Enumeration

### nmap

```bash
nmap -p 21 --script=ftp-anon,ftp-bounce,ftp-libopie,ftp-proftpd-backdoor,ftp-vsftd-backdoor,ftp-vuln-cmd <IP>
```

### Enumerating FTP Anonymously

Check if anonymous access is enabled:

```bash
nc -nv <IP> 21 <<< "USER anonymous"
nc -nv <IP> 21 <<< "PASS guest@"
```

List files and directories in the FTP root directory anonymously:

```text
ftp://<IP>/bin/ls
```

Download a file using anonymous access:

```bash
wget --user=anonymous --password=guest ftp://<IP>/path/to/file.txt
```

---

## 📂 File Upload/Download

### Uploading Files via FTP

Upload a malicious file to the server anonymously:

```text
ftp -inv <IP> <<!
quote USER anonymous
quote PASS guest@
put /local/path/to/malicious.exe /
quit
!
```

### Downloading Files from FTP Server

Downloading sensitive files like passwords or configuration files from the FTP server:

```bash
wget --user=anonymous --password=guest ftp://<IP>/path/to/sensitive/file.txt
```

---

## ⚠️ Potential Risks and Mitigations

> [!WARNING] Anonymous Access Risk
>
> Enabling anonymous access on an FTP server allows unauthorized users to upload files, potentially leading to malicious file uploads or data exfiltration. Ensure that anonymous FTP is disabled unless absolutely necessary.

To mitigate risks:

- Disable anonymous login:
  ```bash
  sudo nano /etc/vsftpd.conf
  ```
  Set `anonymous_enable=NO`.

- Enable SSL/TLS for secure transfers.
- Use SFTP (Secure File Transfer Protocol) instead of plain FTP.

---

# 📌 Common Findings

| Finding | Impact |
|---|---|
| Anonymous Access Enabled | Unauthorized User Uploads / Downloads |
| Missing Permissions Enforcement | Data Leakage, Malware Spread |

---

> [!NOTE]
> Always verify the security configuration of your FTP server to prevent unauthorized access and data breaches.
```

This formatted note adheres strictly to the provided callout schema and Obsidian markdown formatting rules.