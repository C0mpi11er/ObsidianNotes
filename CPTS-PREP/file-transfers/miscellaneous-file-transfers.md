# 🛰️ Secure File Transfer Techniques

## 🔍 Overview of Secure File Transfer Methods

Secure file transfer is essential for maintaining data integrity and confidentiality during remote operations. Below are various methods to securely transfer files in different environments.

---

### 💡 [!ABSTRACT] Using HTTPS with PowerShell Remoting

To ensure secure file transfer using PowerShell remoting, utilize the `New-PSSessionOption` cmdlet with `-UseSSL`. This will establish a secure connection over HTTPS, ensuring that all data is encrypted during transit.

```powershell
$SessionOption = New-PSSessionOption -UseSSL
$Session = New-PSSession -ComputerName DATABASE01 -SessionOption $SessionOption -Port 5986
```

## 🔍 RDP (Remote Desktop Protocol)

RDP allows users to interact with a remote machine and copy files using the clipboard or by mounting local directories.

### 🚀 Method 1: Copy and Paste

**Basic RDP Connection:**
- **rdesktop:**
```bash
rdesktop 10.10.10.132 -d HTB -u administrator -p 'test123'
```
- **xfreerdp:**
```bash
xfreerdp /v:10.10.10.132 /d:HTB /u:administrator /p:'test123'
```

### 📂 Method 2: Mount Local Directory

**Mounting a Linux Folder Using rdesktop:**
```bash
rdesktop 10.10.10.132 -d HTB -u administrator -p 'test123' -r disk:linux='/home/user/rdesktop/files'
```

**Mounting a Linux Folder Using xfreerdp:**
```bash
xfreerdp /v:10.10.10.132 /d:HTB /u:administrator /p:'test123' /drive:linux,/home/plaintext/htb/academy/filetransfer
```

**Access the mounted directory:**
- Navigate to `\\tsclient\linux` in Windows Explorer

### 🛠️ Windows Native RDP Client

Using `mstsc.exe`:
```bash
# Open Remote Desktop Connection
mstsc
# Local Resources tab -> More... -> Drives -> Select drives
```

## 🔍 Advanced RDP Options

**Enable clipboard sharing:**
```bash
xfreerdp /v:10.10.10.132 /u:administrator /p:'test123' /clipboard
```

**Mount multiple drives:**
```bash
xfreerdp /v:10.10.10.132 /u:administrator /p:'test123' /drive:share1,/tmp /drive:share2,/home/user
```

**RDP with custom resolution:**
```bash
xfreerdp /v:10.10.10.132 /u:administrator /p:'test123' /w:1920 /h:1080 /drive:linux,/tmp
```

---

## 🔍 Additional Network Transfer Methods

### 🌐 Using SSH Tunneling

**Forward local port through SSH:**
```bash
ssh -L 8080:localhost:80 user@target-host
```

**Transfer files through tunnel:**
```bash
# After establishing tunnel
curl http://localhost:8080/file.txt -o file.txt
```

### 🔒 Using FTP/SFTP

**Basic FTP transfer:**
```bash
ftp target-host
# ftp> binary
# ftp> put localfile.txt
# ftp> get remotefile.txt
```

**SFTP batch operations:**
```bash
echo "put localfile.txt" > sftp_commands.txt
echo "get remotefile.txt" >> sftp_commands.txt
sftp -b sftp_commands.txt user@target-host
```

### 💾 Using SMB/CIFS

**Mount SMB share:**
```bash
sudo mount -t cifs //target-host/share /mnt/smb -o username=user,password=test123

# Transfer files
cp file.txt /mnt/smb/
cp /mnt/smb/remote_file.txt .

# Unmount when done
sudo umount /mnt/smb
```

---

## 🛡️ Security Considerations

### 🔒 Encryption

**Always prefer encrypted methods:**
- Use HTTPS instead of HTTP
- Use SFTP instead of FTP
- Use SSH tunneling for additional security
- Use Ncat with SSL/TLS

### 🛡️ Network Security

**Firewall considerations:**
- Outbound connections are often less restricted
- Use common ports (80, 443, 53) when possible
- Consider using reverse connections

### 🔍 Data Integrity

**Verify file transfers:**
```bash
# Generate checksum on source
md5sum file.txt > file.txt.md5

# Verify on destination
md5sum -c file.txt.md5
```

**Check file sizes:**
```bash
# Source
ls -la file.txt

# Destination
ls -la file.txt
```

---

## 🛠️ Troubleshooting Common Issues

### 🔍 Netcat Issues

**Connection refused:**
- Check if port is open
- Verify firewall rules
- Try different ports

**Transfer incomplete:**
- Use `-q 0` with original netcat
- Use `--send-only` and `--recv-only` with ncat
- Check file sizes after transfer

### 🔍 PowerShell Remoting Issues

**Access denied:**
- Verify user permissions
- Check if WinRM is enabled
- Verify Remote Management Users group membership

**Connection timeout:**
- Check network connectivity
- Verify WinRM ports (5985/5986)
- Check Windows Firewall settings

### 🔍 RDP Issues

**Authentication failed:**
- Verify credentials
- Check domain settings
- Ensure RDP is enabled

**Drive mounting not working:**
- Check RDP client version
- Verify local permissions
- Try different mount paths

---

## 📝 Best Practices

1. **Choose appropriate method based on environment constraints**
2. **Verify file integrity after transfers**
3. **Use encryption when dealing with sensitive data**
4. **Clean up temporary files and connections**
5. **Document methods that work in specific environments**
6. **Test multiple methods as backup options**
7. **Monitor network traffic to avoid detection**
8. **Use legitimate tools when possible to blend in**

---

## 📚 Key Takeaways

1. **Netcat is versatile** - Works for both directions and can bypass firewalls
2. **PowerShell Remoting** - Powerful for Windows environments with WinRM
3. **RDP file sharing** - Convenient for interactive file transfers
4. **Multiple fallback options** - Always have backup methods ready
5. **Security matters** - Use encrypted methods when possible
6. **Firewall considerations** - Understand network restrictions
7. **Verification important** - Always check file integrity
8. **Environment awareness** - Different methods work in different scenarios

---

## 🔍 References

- [Netcat Manual](https://nc110.sourceforge.io/)
- [Ncat Guide](https://nmap.org/ncat/guide/)
- [PowerShell Remoting Guide](https://docs.microsoft.com/en-us/powershell/scripting/learn/remoting/running-remote-commands)
- [xfreerdp Manual](https://github.com/FreeRDP/FreeRDP/wiki/CommandLineInterface)
- [Windows RDP Documentation](https://docs.microsoft.com/en-us/windows-server/remote/remote-desktop-services/)
- [SSH Tunneling Guide](https://www.ssh.com/academy/ssh/tunneling/example)