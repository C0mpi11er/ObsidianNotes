---

# 📁 Windows Management Instrumentation (WMI) Cheat Sheet

---

> [!info] 🧠 What is WMI?
>
> ```bash
> A subsystem that provides a standardized interface for managing data and operations on Windows systems.
> ```
> 
> 💡 Key Characteristics:
>
> * **Standardized**: Based on the Common Information Model (CIM).
> * **Powerful**: Can query system info, manage processes, and execute commands.
> * **Remote Capability**: Allows administrators (and attackers) to manage remote systems.
>
> ✔ Think of it as **the "Search Engine" and "Control Panel" for Windows internals.**

---

# 🏗️ Core Components

---

| Component | Description |
| :--- | :--- |
| **WMI Repository** | A database that stores static data and class definitions. |
| **WMI Service** | The intermediary (`Winmgmt`) that handles requests between users and providers. |
| **Providers** | Agents that monitor specific hardware or software and return data to WMI. |
| **Namespaces** | Logical groupings of classes (e.g., `ROOT\CIMV2` is the most common). |

---

# 🔍 Footprinting & Enumeration

---

> [!success] 📡 Key Commands
>
> ```powershell
> # 1. List all available WMI classes in the default namespace
> Get-WmiObject -List
> 
> # 2. Get OS details using WMI
> Get-WmiObject -Class Win32_OperatingSystem
> 
> # 3. Query remote systems (requires permissions)
> Get-WmiObject -Class Win32_Process -ComputerName <TargetIP>
> ```
> 
> 💡 Key Tools:
>
> * **wmic.exe**: The legacy command-line utility (deprecated but still common).
> * **PowerShell**: The modern way to interact with WMI/CIM.

---

# 🔐 Authentication & Security

---

> [!info] 🆔 Access Control
>
> 1. **Local Admin**: By default, you need local administrative privileges to query sensitive WMI data or execute methods.
> 2. **DCOM/RPC**: WMI traditionally uses DCOM and RPC for remote communication (Ports 135 and dynamic ports).
> 3. **WinRM**: Newer WMI/CIM calls often travel over WinRM (Ports 5985/5986).
>
> 💡 **The Risk:** If an attacker gains a local admin's credentials, they can use WMI to move laterally and execute code on any machine in the network.

---

# ⚠️ Common Exploitation Paths

---

> [!danger] 🚀 Remote Code Execution (RCE)
> One of the most common uses for WMI in lateral movement.
>
> ```bash
> # Using Impacket's wmiexec.py from Linux
> wmiexec.py Administrator:Password123@<TargetIP>
> ```
>
> 💡 How it works:
>
> * It creates a new process (like `cmd.exe`) via the `Win32_Process` class to execute commands.

---

> [!warning] 🕵️ Persistence via WMI Events
> Attackers can create **WMI Event Subscriptions** that trigger a malicious script when a certain condition is met (e.g., system uptime reaches 5 minutes).
>
> 💡 Why it's scary:
>
> * It is fileless and lives entirely within the WMI repository.

---

# ⚡ Real-World Workflow

---

> [!success] 🧠 Interaction Flow
>
> ```bash
> 1. Identify Target (Port 135/445/5985 open)
> 2. Authenticate (Local Admin credentials)
> 3. Enumerate (Check installed software, running processes)
> 4. Execute (Start a reverse shell or dump credentials)
> 5. Persist (Set up a WMI Event Filter/Consumer)
> ```
>
> 💡 WMI is a "living-off-the-land" (LotL) technique because it uses legitimate system tools to perform malicious actions.

---

# 🧩 Mental Model

---

> [!quote] 🎯 The Windows Librarian
>
> 
>
> ```bash
> Repository → The Library Books
> WMI Service → The Librarian
> Class → A specific book (e.g., "The Book of Processes")
> Property → A page in that book (e.g., "Process ID")
> ```
>
> 💡 If you know how to talk to the **Librarian**, you can find out anything about the building—or even ask them to **redecorate** (run code).
```