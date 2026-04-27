> [!info] **What is a Shell?**  
> A **shell** is a program that allows users to interact with an operating system via commands and text output.  
> Examples: **Bash, Zsh, cmd, PowerShell**

---

> [!success] **Shell in Pentesting**
> 
> - A shell is usually the **result of successful exploitation**
> - It gives **interactive control over a target system**
> - Common phrases:
>     - _“I caught a shell”_
>     - _“I popped a shell”_
>     - _“I’m in”_  
>         → Meaning: attacker has gained **remote access to the OS**

---

> [!important] **Why Getting a Shell Matters**
> 
> - Direct access to:
>     - OS commands
>     - File system
> - Enables:
>     - 🔍 Enumeration
>     - ⬆️ Privilege escalation
>     - 🔁 Pivoting
>     - 📂 File transfer
>     - 📤 Data exfiltration
> - Allows **persistence** (stay longer on system)
> - CLI access is:
>     - ⚡ Faster
>     - 🤖 Easier to automate
>     - 🕵️ Harder to detect than GUI (RDP/VNC)

---

> [!abstract] **Shell Perspectives**  
> **1. Computing**
> 
> - CLI interface for system interaction
> - (Bash, Zsh, cmd, PowerShell)
> 
> **2. Exploitation & Security**
> 
> - Result of exploiting a vulnerability
> - Example: exploiting **EternalBlue** → remote shell
> 
> **3. Web**
> 
> - **Web shell** = script uploaded via vulnerability
> - Controlled via browser
> - Can:
>     - Execute commands
>     - Read/write files
>     - Control server

---

> [!tip] **Payloads = How You Get Shells**  
> A **payload** is code used to achieve a goal (e.g., get a shell)

**Different meanings:**

- 🌐 Networking → data inside packets
- 💻 Computing → action part of instructions
- 👨‍💻 Programming → carried data
- 🔓 Security → **malicious code to exploit systems**

---

> [!warning] **In Pentesting Context**
> 
> - Payloads are used to:
>     - Exploit vulnerabilities
>     - Deliver shells
>     - Gain remote access
> - Can include:
>     - Reverse shells
>     - Bind shells
>     - Malware (e.g., ransomware)

---

> [!summary] **Core Idea**
> 
> - **Exploit → Payload → Shell → Full Control**
> - If you don’t get a shell, your attack is **very limited**

