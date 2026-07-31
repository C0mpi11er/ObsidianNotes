# 🛰️ Kerberos "Double Hop" Problem Overview

[!ABSTRACT] The Kerberos double hop problem arises when a user attempts to authenticate across multiple systems within an Active Directory domain without proper delegation configurations, leading to authentication failures. This document provides comprehensive guidance on understanding the technical underpinnings of this issue and offers practical workarounds for penetration testing scenarios.

---

## 🔍 Understanding Kerberos Authentication

### 🌐 Network Protocols

#### **Kerberos Protocol**
- **Initial Request**: AS (Authentication Service) ticket requested from KDC.
- **Service Ticket**: TGS (Ticket Granting Service) ticket obtained to access services.
- **Delegation Limitations**: Kerberos tickets cannot be delegated beyond a single hop due to security constraints.

#### **NTLM Authentication**
- **SMB Protocol**: Uses NTLM for authentication between client and server.
- **Credential Handling**: Passes username and password directly, avoiding Kerberos ticket limitations but introducing potential credential exposure risks.

---

## 🛠️ Practical Workarounds

### 🔒 Credential Passing Techniques

#### **PSCredential Object**
```powershell
$SecPassword = ConvertTo-SecureString "password" -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential("domain\user", $SecPassword)
Invoke-Command -ComputerName target-server -ScriptBlock { param($cred) whoami /user } -ArgumentList $Cred
```

#### **PSSession Configuration**
```powershell
$UserCredential = Get-Credential domain\user
Enable-PsSessionConfiguration -Name Microsoft.PowerShell -ShowSecurityDescriptorUI -Force
Invoke-Command -ComputerName target-server -ScriptBlock { whoami /user } -Credential $UserCredential
```

### 🚀 Alternative Solutions

#### **CredSSP (Credential Security Support Provider)**
```powershell
# Enable CredSSP on client
Enable-WSManCredSSP -Role Client -DelegateComputer "target-server"

# Enable CredSSP on server
Enable-WSManCredSSP -Role Server

# Connect using CredSSP
Enter-PSSession -ComputerName "target" -Credential $cred -Authentication CredSSP
```

#### **Port Forwarding Solutions**
```bash
# SSH Tunnel for RDP
ssh -L 3389:target-dc:3389 user@jump-host

# Chisel/SocksOverRDP
./chisel server -p 8080 --reverse
./chisel client target-ip:8080 R:1080:socks
```

### 💥 Process Injection Techniques

#### **Token Impersonation**
```powershell
Invoke-TokenManipulation -CreateProcess "cmd.exe" -Username "target-user"
```

---

## 🛡️ Detection and Defensive Measures

### 🔍 Monitoring Double Hop Workarounds

#### **PSSession Configuration Detection**
```powershell
Get-PSSessionConfiguration
```

#### **CredSSP Usage Detection**
```powershell
# Event ID monitoring:
# 4103 - PowerShell Script Block Logging
# 4104 - PowerShell Script Block Logging (detailed)
# 4624 - Account logon (Network, Type 3)
# 4648 - Logon with explicit credentials
```

### 🛠️ PowerShell Logging Enhancement

#### **Script Block Logging**
```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging" -Name "EnableScriptBlockLogging" -Value 1
Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\PowerShell\ScriptBlockLogging" -Name "EnableScriptBlockInvocationLogging" -Value 1
```

#### **Module Logging**
```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\PowerShell\ModuleLogging" -Name "EnableModuleLogging" -Value 1
Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\PowerShell\ModuleLogging\ModuleNames" -Name "*" -Value "*"
```

### 📡 Network Monitoring

#### **Kerberos Traffic Analysis**
- **AS-REQ/AS-REP**: Initial authentication requests
- **TGS-REQ/TGS-REP**: Service ticket requests
- **Unusual patterns**: Multiple service ticket requests from single host

---

## 🚀 Advanced Attack Chains

### 💥 Double Hop in Complex Scenarios

#### **Multi-Domain Trust Exploitation**
```
Attack Host → Domain A Server → Domain B Controller → Domain C Resources
```

**Challenges**:
- **Cross-domain authentication**: Different Kerberos realms
- **Trust relationship dependencies**: Transitive vs. non-transitive trusts
- **Delegation configurations**: Per-domain delegation settings

#### **Cloud Hybrid Environments**
```
On-Premises → Azure AD Connect → Azure AD → Cloud Resources
```

**Considerations**:
- **Authentication protocols**: Kerberos vs. SAML vs. OAuth
- **Credential synchronization**: Password hash sync vs. pass-through authentication
- **Hybrid identity**: On-premises accounts with cloud access

### 💡 Automation and Scripting

#### **Automated Workaround Implementation**
```powershell
function Invoke-DoubleHopWorkaround {
    param(
        [string]$ComputerName,
        [PSCredential]$Credential,
        [string]$Command
    )
    
    # Create PSCredential object
    $SecPassword = ConvertTo-SecureString $Credential.GetNetworkCredential().Password -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential($Credential.UserName, $SecPassword)
    
    # Execute command with credential passing
    Invoke-Command -ComputerName $ComputerName -Credential $Credential -ScriptBlock {
        param($Cred, $Cmd)
        Invoke-Expression "$Cmd -Credential `$Cred"
    } -ArgumentList $Cred, $Command
}
```

#### **PowerShell Empire Integration**
```powershell
usemodule credentials/mimikatz/golden_ticket
set Credential domain\user:password
set Target target-server
execute
```

---

## 📊 Key Takeaways

### 🛠️ Technical Understanding
1. **Kerberos vs. NTLM**: Fundamental difference in credential handling.
2. **Ticket mechanics**: TGT vs. TGS ticket usage and limitations.
3. **Authentication delegation**: Constrained, unconstrained, and resource-based delegation.
4. **Network protocols**: WinRM, RDP, and their authentication mechanisms.

### 🛠️ Practical Solutions
1. **PSCredential Object**: Universal solution for Evil-WinRM and command-line scenarios.
2. **PSSession Configuration**: Persistent solution for GUI-accessible Windows hosts.
3. **Alternative methods**: CredSSP, port forwarding, and process injection techniques.
4. **Tool compatibility**: Understanding which tools support which workarounds.

### 🛠️ Operational Considerations
1. **Attack platform**: Linux vs. Windows attack host capabilities.
2. **Target environment**: Domain topology and delegation configurations.
3. **Detection risk**: Monitoring and logging considerations.
4. **Persistence vs. stealth**: Balancing effectiveness with operational security.

### 💡 Professional Application
- **Red team operations**: Realistic attack simulation with proper lateral movement.
- **Penetration testing**: Comprehensive domain exploitation methodology.
- **Security assessment**: Understanding authentication boundaries and limitations.
- **Incident response**: Recognizing double hop exploitation techniques.

**🔑 Complete mastery of Kerberos "Double Hop" Problem - from technical understanding through practical workarounds to advanced attack chains - representing essential Active Directory lateral movement expertise for enterprise penetration testing!**

---