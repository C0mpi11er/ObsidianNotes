

> [!info] **Anatomy of a Shell (Big Picture)**  
> A working shell environment =  
> **Terminal Emulator + Command Language Interpreter + Operating System**

---

> [!abstract] **Terminal Emulator**
> 
> - An application that lets you **interact with a shell**
> - Provides the **interface (window + input/output)**

**Common examples:**

- Windows → Windows Terminal, cmder, PuTTY
- Linux → GNOME Terminal, MATE Terminal, xterm, Konsole
- macOS → Terminal, iTerm2
- Cross-platform → kitty, Alacritty

> [!tip]  
> Choice of terminal = **personal preference + workflow**, not “right or wrong”

---

> [!important] **Command Language Interpreter (Shell Language)**
> 
> - Translates your commands → OS actions
> - Acts like a **real-time translator** between you and the system
> - Also called:
>     - Shell scripting language
>     - Command interpreter

> [!example]  
> Examples:
> 
> - Bash
> - PowerShell
> - Zsh
> - cmd

---

> [!success] **How CLI Actually Works**  
> CLI =  
> **Terminal Emulator (UI)** + **Shell (Interpreter)** + **Operating System (Execution)**

---

> [!warning] **Why It Matters in Pentesting**
> 
> - Different shells = different commands & syntax
> - You must:
>     - Identify the shell
>     - Adapt your commands/scripts
> - Wrong assumption = **commands fail**

---

> [!tip] **Identifying the Shell**  
> **1. Prompt symbol**
> 
> - `$` → Bash / Unix-like shells
> - `>` → PowerShell / cmd

**2. Running processes**

ps

**3. Environment variables**

env

Example:

SHELL=/bin/bash

---

> [!example] **Behavior Example**
> 
> - Typing random command → “command not found”  
>     → Response comes from the **shell interpreter (e.g., Bash)**

---

> [!important] **Same Terminal, Different Shells**
> 
> - One terminal emulator can run multiple shells
> - Example:
>     - MATE Terminal running:
>         - Bash
>         - PowerShell

> [!insight]  
> Terminal ≠ Shell  
> (This is a common beginner mistake)

---

> [!question] **Why Use Different Shells?**
> 
> - Different capabilities:
>     - PowerShell → strong for Windows automation
>     - Bash → strong for Linux scripting
> - Compatibility with target system
> - Personal workflow preference

---

> [!summary] **Core Takeaways**
> 
> - Terminal = interface
> - Shell = interpreter
> - OS = executes commands
> - In pentesting:
>     - Always identify the shell
>     - Adapt commands accordingly
>     - Master multiple shells for flexibility