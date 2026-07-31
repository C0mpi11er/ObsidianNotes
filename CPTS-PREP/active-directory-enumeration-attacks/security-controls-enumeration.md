# 🛰️ Active Directory Security Controls Enumeration

## 🔍 Objective Overview
[!ABSTRACT] The script provided aims to comprehensively assess security controls in an environment, focusing on Windows Defender, AppLocker, PowerShell language mode, and LAPS (Local Administrator Password Solution). This assessment is crucial for understanding the defensive landscape before deploying tools or techniques.

---

## 📑 Table of Contents

- [Enumeration Targets](#-%F0%9D%84%B7-enumeration-targets)
  - [Windows Defender](#-%E2%A6%93-windows-defender)
  - [AppLocker](#-%E2%A6%95-applocker)
  - [PowerShell Constrained Language Mode](#-%E2%A6%91-powershell-constrained-language-mode)
  - [LAPS (Local Administrator Password Solution)](#-%E2%A6%94-laps-local-administrator-password-solution)
- [Detailed Commands and Scripts](#-%F0%9D%85%83-detailed-commands-and-scripts)
- [Key Attack Implications](#-%F0%9D%85%B7-key-attack-implications)
  - [Security Control Impact Matrix](#-%E2%A6%BA-security-control-impact-matrix)
  - [Adaptation Strategies](#-%E2%A6%BB-adaptation-strategies)
- [Quick Reference Commands](#-%F0%9D%85%BD-quick-reference-commands)
- [Key Takeaways](#-%E2%A6%B3-key-takeaways)

---

## 📝 Enumeration Targets

### 🔍 Windows Defender
[!INFO] This section checks the status of key features in Windows Defender.

```powershell
Get-MpComputerStatus | Select-Object RealTimeProtectionEnabled, BehaviorMonitorEnabled, OnAccessProtectionEnabled
```

### 🔍 AppLocker
[!WARNING] Use with caution; some organizations have strict logging mechanisms.

```powershell
Get-AppLockerPolicy -Effective
Find-LAPSDelegatedGroups
```

### 🔍 PowerShell Constrained Language Mode

#### 🛡️ Restricted and NoLanguage Modes

Restricted language mode restricts access to certain .NET classes, COM objects, and other potentially dangerous features. `NoLanguage` mode is even more restrictive.

```powershell
$ExecutionContext.SessionState.LanguageMode
```

#### 🛠️ Bypassing Constrained Language Mode

To determine if you are in a restricted environment:

```powershell
try { New-Object -ComObject Excel.Application } catch { "COM Blocked" }
```

### 🔍 LAPS (Local Administrator Password Solution)

LAPS stores and manages local administrator passwords on Windows systems. This script helps identify where it is deployed.

```powershell
Find-LAPSDelegatedGroups
Get-ADObject -Filter 'objectClass -eq "msFVE-RecoveryInformation"' -Properties *
```

---

## 📄 Detailed Commands and Scripts

### 🚀 Comprehensive Enumeration Script

```powershell
function Invoke-SecurityControlsEnum {
    Write-Host "`n=== SECURITY CONTROLS ENUMERATION ===" -ForegroundColor Cyan
    Write-Host "Starting comprehensive security assessment...`n" -ForegroundColor Yellow
    
    # Windows Defender
    Write-Host "[*] Checking Windows Defender..." -ForegroundColor Green
    try {
        $defender = Get-MpComputerStatus -ErrorAction Stop
        Write-Host "  [+] Windows Defender Status:" -ForegroundColor White
        Write-Host "      Real-time Protection: $($defender.RealTimeProtectionEnabled)" -ForegroundColor $(if($defender.RealTimeProtectionEnabled){"Red"}else{"Green"})
        Write-Host "      Behavior Monitor: $($defender.BehaviorMonitorEnabled)" -ForegroundColor $(if($defender.BehaviorMonitorEnabled){"Red"}else{"Green"})
        Write-Host "      On-Access Protection: $($defender.OnAccessProtectionEnabled)" -ForegroundColor $(if($defender.OnAccessProtectionEnabled){"Red"}else{"Green"})
    }
    catch {
        Write-Host "  [-] Cannot access Windows Defender status" -ForegroundColor Red
    }
    
    # AppLocker
    Write-Host "`n[*] Checking AppLocker..." -ForegroundColor Green
    try {
        $applocker = Get-AppLockerPolicy -Effective -ErrorAction Stop
        if ($applocker) {
            Write-Host "  [+] AppLocker is configured" -ForegroundColor Red
            $rules = $applocker.RuleCollections
            foreach ($collection in $rules) {
                $denyRules = $collection | Where-Object {$_.Action -eq "Deny"}
                if ($denyRules) {
                    Write-Host "      Blocked: $($denyRules.Name -join ', ')" -ForegroundColor Red
                }
            }
        }
    }
    catch {
        Write-Host "  [+] AppLocker not configured" -ForegroundColor Green
    }
    
    # PowerShell Language Mode
    Write-Host "`n[*] Checking PowerShell Language Mode..." -ForegroundColor Green
    $langMode = $ExecutionContext.SessionState.LanguageMode
    Write-Host "  Language Mode: $langMode" -ForegroundColor $(if($langMode -eq "FullLanguage"){"Green"}else{"Red"})
    
    # LAPS
    Write-Host "`n[*] Checking LAPS..." -ForegroundColor Green
    try {
        if (Get-Command Find-LAPSDelegatedGroups -ErrorAction SilentlyContinue) {
            $lapsGroups = Find-LAPSDelegatedGroups
            if ($lapsGroups) {
                Write-Host "  [+] LAPS is deployed" -ForegroundColor Yellow
                Write-Host "      Delegated groups found: $($lapsGroups.Count)" -ForegroundColor White
            }
        } else {
            Write-Host "  [!] LAPSToolkit not available for detailed enumeration" -ForegroundColor Yellow
        }
    }
    catch {
        Write-Host "  [-] LAPS enumeration failed" -ForegroundColor Red
    }
    
    # Additional checks
    Write-Host "`n[*] Additional Security Checks..." -ForegroundColor Green
    
    # Check UAC
    $uac = Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" -Name "EnableLUA" -ErrorAction SilentlyContinue
    Write-Host "  UAC Enabled: $($uac.EnableLUA -eq 1)" -ForegroundColor $(if($uac.EnableLUA -eq 1){"Red"}else{"Green"})
    
    # Check Windows Firewall
    try {
        $firewall = Get-NetFirewallProfile -ErrorAction Stop
        $enabled = $firewall | Where-Object {$_.Enabled -eq $true}
        Write-Host "  Windows Firewall Profiles Enabled: $($enabled.Count)/3" -ForegroundColor $(if($enabled.Count -gt 0){"Red"}else{"Green"})
    }
    catch {
        Write-Host "  Windows Firewall: Cannot determine status" -ForegroundColor Yellow
    }
    
    Write-Host "`n=== ASSESSMENT COMPLETE ===`n" -ForegroundColor Cyan
}

# Execute the assessment
Invoke-SecurityControlsEnum
```

---

## 📊 Key Attack Implications

### 🔍 Security Control Impact Matrix

| **Control** | **High Impact** | **Medium Impact** | **Low Impact** |
|-------------|-----------------|-------------------|----------------|
| **Windows Defender** | Real-time scanning active | Behavior monitoring enabled | On-access protection disabled |
| **AppLocker** | PowerShell/cmd blocked | Script execution restricted | Default rules only |
| **Constrained Language** | NoLanguage/Restricted | ConstrainedLanguage | FullLanguage |
| **LAPS** | Fully deployed | Partial deployment | Not deployed |

### 🛠️ Adaptation Strategies

#### 🔐 High Security Environment
```powershell
# When multiple controls are active:
- Use living-off-the-land techniques
- Leverage built-in Windows tools
- Focus on abuse of legitimate functionality
- Employ memory-only payloads
- Use signed binaries and DLL hijacking
```

#### 🛠️ Medium Security Environment
```powershell
# When some controls are present:
- Test specific bypass techniques
- Use alternative execution methods
- Leverage trusted directories/binaries
- Employ obfuscation techniques
```

#### 🔓 Low Security Environment
```powershell
# When few controls are active:
- Standard PowerShell tools available
- Direct tool execution possible
- Minimal evasion required
- Focus on speed and efficiency
```

---

## ⚡ Quick Reference Commands

### 🔍 Rapid Assessment
```powershell
# One-liner security assessment
Write-Host "Defender: $((Get-MpComputerStatus).RealTimeProtectionEnabled) | Language: $($ExecutionContext.SessionState.LanguageMode) | AppLocker: $(if(Get-AppLockerPolicy -Effective -ErrorAction SilentlyContinue){'Enabled'}else{'Disabled'})"

# Test common restrictions
Test-Path "C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe" -ErrorAction SilentlyContinue
$ExecutionContext.SessionState.LanguageMode
(Get-MpComputerStatus).RealTimeProtectionEnabled
```

### 🛠️ Bypass Testing
```powershell
# PowerShell alternative locations
%SystemRoot%\SysWOW64\WindowsPowerShell\v1.0\powershell.exe
%SystemRoot%\system32\WindowsPowerShell\v1.0\PowerShell_ISE.exe

# Test COM access in Constrained Language
try { New-Object -ComObject Excel.Application } catch { "COM Blocked" }

# Test .NET reflection
try { [System.IO.File]::ReadAllText("C:\Windows\System32\drivers\etc\hosts") } catch { "Reflection Blocked" }
```

---

## 📚 Key Takeaways

### 💡 Understanding the Environment
- **Security Controls**: Assessing security controls like Windows Defender, AppLocker, and LAPS provides a clear picture of defensive measures in place.
- **Language Mode**: Knowing whether PowerShell is operating under constrained language mode can help tailor your approach to bypass restrictions.

---

## 📄 Appendix

### 🔍 Additional Checks
```powershell
Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System" -Name "EnableLUA"
```

This command checks if User Account Control (UAC) is enabled.

### 🔍 Firewall Status
```powershell
Get-NetFirewallProfile | Format-Table Name,Enabled -AutoSize
```

This command provides a summary of the firewall status across different profiles (Domain, Private, Public).

---

## 📖 References

- **Windows Defender**: Microsoft Docs on Get-MpComputerStatus.
- **AppLocker**: Microsoft Docs on AppLocker Policy.
- **PowerShell Language Mode**: PowerShell documentation on ConstrainedLanguage.
- **LAPS**: Microsoft TechNet documentation.

--- 

This script and guide are designed to help you quickly understand the security posture of an environment, enabling more targeted and effective penetration testing or red team operations. 

# 🛠️ End

---

[!ABSTRACT] This document provides a comprehensive approach to assessing key security controls in Active Directory environments. It helps ensure that you have a clear understanding of the defensive measures before conducting any actions. Always use these tools responsibly and ethically, respecting all legal and organizational boundaries.

--- 

# 🛠️ Final Notes

- Ensure that you have proper authorization before running any scripts on production systems.
- This script is intended for educational purposes and to improve security by identifying weaknesses.
- Regular updates and enhancements are recommended based on evolving security landscapes. 

---

[!ABSTRACT] By leveraging this assessment, you can tailor your strategies to overcome challenges posed by different levels of security controls in Active Directory environments.

--- 
# 🛠️ END OF DOCUMENT

--- 

Please let me know if there's anything else you need or any specific scenarios you want to cover. I'm here to help! 😊👍