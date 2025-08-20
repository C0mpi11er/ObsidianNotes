
Here’s a detailed, well-organized Obsidian note for the **Privilege Escalation Enumeration Tools** lecture, structured for clarity and quick reference.

You can copy-paste this directly into your Obsidian vault:

---

## 🛠 Privilege Escalation Enumeration Tools (Windows)

> Use these tools to automate system enumeration and find potential privilege escalation vectors. While they **speed up the process**, always validate results manually—**automation can miss things**.

---

## 📦 Tool 1: **WinPEAS**

### 🔍 What It Does

* Gathers system info, privileges, services, scheduled tasks, registry misconfigs, and more.
* Looks for common **privilege escalation** paths.

### 💡 How to Use

1. **Download** from [GitHub - WinPEAS](https://github.com/carlospolop/PEASS-ng)
2. **Upload** to the target machine (binary or `.bat` version)

### ✅ Run & Save Output

```cmd
C:\> winpeas.exe > outputfile.txt
```

### ⚠️ Notes

* Output is **verbose** — use redirection for easier analysis.
* Can be detected by AV tools.

---

## 📦 Tool 2: **PrivescCheck (PowerShell)**

### 🔍 What It Does

* Checks for **local privilege escalation** techniques via PowerShell.
* Safer in restricted environments (e.g., no binary upload).

### 💡 How to Use

1. **Download** from [GitHub - PrivescCheck](https://github.com/itm4n/PrivescCheck)
2. **Bypass execution policy** and run script:

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
. .\PrivescCheck.ps1
Invoke-PrivescCheck
```

### ✅ Output

* Organized, readable results.
* Checks registry, token privileges, service misconfigs, etc.

---

## 📦 Tool 3: **WES-NG (Windows Exploit Suggester – NG)**

### 🔍 What It Does

* Matches **missing patches** from `systeminfo` with known exploits.
* Useful for **remote, low-noise enumeration** (runs on attacker machine).

### 💡 How to Use

1. **On target system**, gather info:

```cmd
C:\> systeminfo > systeminfo.txt
```

2. **Transfer `systeminfo.txt` to attacker machine**

3. **On attacker machine**, install and update WES-NG:

```bash
git clone https://github.com/bitsadmin/wesng.git
cd wesng
python3 wes.py --update
```

4. **Run WES-NG**:

```bash
python3 wes.py systeminfo.txt
```

### ⚠️ Notes

* Output suggests exploits based on OS version and missing patches.
* Less detectable than in-place tools like WinPEAS.

---

## 📦 Tool 4: **Metasploit - Local Exploit Suggester**

### 🔍 What It Does

* Suggests local exploits via **Meterpreter** session.

### 💡 How to Use

```bash
use post/multi/recon/local_exploit_suggester
set SESSION <session_id>
run
```

### ✅ Output

* Returns known privilege escalation modules that match target.
* Easy to run after initial foothold with Meterpreter.

---

## ✅ Summary Comparison

| Tool         | Runs on Target? | Output Format | Detection Risk | Language   | Usage Type                     |
| ------------ | --------------- | ------------- | -------------- | ---------- | ------------------------------ |
| WinPEAS      | Yes             | CLI/Text      | Medium-High    | Binary/BAT | Full Enum                      |
| PrivescCheck | Yes             | PowerShell    | Low-Medium     | PowerShell | Full Enum                      |
| WES-NG       | No (local)      | CLI/Text      | Low            | Python     | Patch-based Exploit Suggestion |
| Metasploit   | Yes (via shell) | Meterpreter   | Medium         | Ruby       | Exploit Suggestion             |

---

## 🧠 Tips for Real-World Use

* 🪪 Always start with **manual checks**, especially `whoami /priv`, services, and scheduled tasks.
* 📤 If AV is strong, prefer **PowerShell-based** or **remote tools**.
* 🔒 Clean up any uploaded scripts or output files to avoid detection.
* 🧾 Always log enumeration outputs for offline analysis.

---

In this room, we have introduced several privilege escalation techniques available in Windows systems. These techniques should provide you with a solid background on the most common paths attackers can take to elevate privileges on a system. Should you be interested in learning about additional techniques, the following resources are available:

    PayloadsAllTheThings - Windows Privilege Escalation
    Priv2Admin - Abusing Windows Privileges
    RogueWinRM Exploit
    Potatoes
    Decoder's Blog
    Token Kidnapping
    Hacktricks - Windows Local Privilege Escalation
