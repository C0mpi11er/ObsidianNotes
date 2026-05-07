
---

# 🌐 Windows Remote Management Cheat Sheet

---

> [!info] 🧠 What is Windows Remote Management?
>
> ```bash
> Remote administration of Windows systems over the network
> ```
>
> 💡 Allows administrators (and attackers) to:
>
> * Access systems remotely
> * Execute commands
> * Manage services and users
>
> ✔ Common in:
>
> * Windows Server environments
> * Active Directory networks
> * Enterprise infrastructure

---

> [!tip] 🔧 Core Idea
>
> ```bash
> Attacker ↔ Remote Windows Services
> ```
>
> 💡 Main protocols:
>
> * RDP → GUI access
> * WinRM → Command execution
> * WMI → Deep system control
>
> ✔ Think of it as **multiple remote entry points**

---

# 🖥️ RDP (Remote Desktop Protocol)

---

> [!info] 📡 Default Port
>
> ```bash
> TCP/UDP 3389
> ```
>
> 💡 Used for:
>
> * Full desktop access
> * Remote GUI interaction

---

> [!tip] 🧠 How it Works
>
> ```bash
> Client ↔ Encrypted GUI Session ↔ Windows Host
> ```
>
> 💡 Lets you:
>
> * Control mouse & keyboard
> * Run applications visually

---

> [!success] 🔗 Connect to RDP
>
> ```bash
> xfreerdp /u:user /p:password /v:<target>
> ```
>
> 💡 Gives full desktop access

---

> [!info] 🔍 Enumeration
>
> ```bash
> nmap -sV -sC -p3389 --script rdp* <target>
> ```
>
> 💡 Reveals:
>
> * Hostname
> * OS version
> * NLA status

---

> [!warning] ⚠️ Common Weaknesses
>
> ```bash
> Weak passwords / No NLA / Self-signed certs
> ```
>
> 💡 Leads to:
>
> * brute-force attacks
> * MITM risks
> * credential theft

---

# ⚙️ WinRM (Windows Remote Management)

---

> [!info] 📡 Default Ports
>
> ```bash
> 5985 (HTTP) / 5986 (HTTPS)
> ```
>
> 💡 Used for:
>
> * Remote PowerShell execution
> * Command-line management

---

> [!tip] 🧠 Core Concept
>
> ```bash
> SOAP-based remote command execution
> ```
>
> 💡 Enables:
>
> * Remote shell access
> * Script execution
> * Admin automation

---

> [!success] 🔗 Connect with Evil-WinRM
>
> ```bash
> evil-winrm -i <target> -u user -p password
> ```
>
> 💡 Drops you into:
>
> * interactive PowerShell session

---

> [!info] 🔍 Enumeration
>
> ```bash
> nmap -sV -p5985,5986 <target>
> ```
>
> 💡 Look for:
>
> * HTTP (less secure)
> * Open access points

---

> [!warning] ⚠️ Common Weaknesses
>
> ```bash
> HTTP enabled / credential reuse
> ```
>
> 💡 Leads to:
>
> * easy shell access
> * lateral movement

---

# 🧬 WMI (Windows Management Instrumentation)

---

> [!info] 📡 Ports
>
> ```bash
> TCP 135 → Random high ports
> ```
>
> 💡 Used for:
>
> * System management
> * Remote execution
> * Monitoring

---

> [!tip] 🧠 Core Concept
>
> ```bash
> Deep system-level control interface
> ```
>
> 💡 Allows:
>
> * command execution
> * system queries
> * configuration changes

---

> [!success] 🔗 Execute Commands
>
> ```bash
> wmiexec.py user:password@<target> "whoami"
> ```
>
> 💡 Returns:
>
> * command output remotely

---

> [!warning] ⚠️ Common Weaknesses
>
> ```bash
> Overprivileged access / credential reuse
> ```
>
> 💡 Leads to:
>
> * full system compromise
> * stealthy lateral movement

---

# 🔍 Enumeration Strategy

---

> [!success] 🧠 Initial Scan
>
> ```bash
> nmap -p 3389,5985,5986,135 <target>
> ```
>
> 💡 Identifies:
>
> * available remote services

---

> [!tip] 🎯 Decision Flow
>
> ```bash
> RDP → GUI access
> WinRM → shell access
> WMI → command execution
> ```
>
> 💡 Choose based on:
>
> * open ports
> * available credentials

---

# ⚠️ Common Misconfigurations

---

> [!danger] 🔓 Weak Credentials
>
> ```bash
> Simple passwords / reused credentials
> ```
>
> 💡 Impact:
>
> * immediate access

---

> [!warning] 🔐 Poor Encryption
>
> ```bash
> HTTP WinRM / weak RDP security
> ```
>
> 💡 Risk:
>
> * interception
> * MITM attacks

---

> [!warning] ⚙️ Default Exposure
>
> ```bash
> Services enabled by default
> ```
>
> 💡 Risk:
>
> * unnecessary attack surface

---

# ⚡ Real-World Workflow

---

> [!success] 🧠 Attack Flow
>
> ```bash
> 1. Scan for ports
> 2. Identify services (RDP/WinRM/WMI)
> 3. Test credentials
> 4. Gain shell (WinRM/WMI)
> 5. Escalate privileges
> 6. Move laterally
> ```
>
> 💡 This is standard Windows exploitation flow

---

# 🧩 Mental Model

---

> [!quote] 🎯 Think Like This
>
> ```bash
> RDP → Screen
> WinRM → Shell
> WMI → Control
> ```
>
> 💡 Each protocol gives a different level of access:
>
> * visual control
> * command execution
> * deep system manipulation

---

