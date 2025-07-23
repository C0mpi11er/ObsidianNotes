# Linked notes


![[linux_priv.png]]



🕵️ Post-Exploitation Enumeration (Linux)
🔧 System Identification

    hostname: Get the machine’s hostname; may hint at its role.

    uname -a: Kernel version & architecture; useful for privesc.

    /proc/version: Kernel & compiler info (e.g., GCC).

    /etc/issue: OS banner (may be modified).

👥 User & Permission Info

    id: Show current user and group memberships.

    /etc/passwd: List system users.

    sudo -l: List commands current user can run as root.

    history: Check previous commands (may leak creds).

📂 File System & Privileges

    ls -la: View hidden files & permissions.

    find: Locate interesting files:

        -perm 0777, -perm -u=s: World-writable or SUID files

        -user <user>, -name <file>, -mtime, -size, etc.

        2>/dev/null: Suppress errors for cleaner output

🧠 Environment

    env: Check for scripting languages or compilers in $PATH.

📡 Network Info

    ifconfig, ip a: Network interfaces

    ip route: Routing info

    netstat (or ss):

        -a, -l, -t, -u, -s, -tp: Listening ports, connections, stats

🧾 Processes

    ps aux: Running processes (user, PID, CMD)

    ps axjf: Process tree



#Tools For LPE(Linux Prov Escalation)

- **LinPeas**: [https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS)
- **LinEnum:** [https://github.com/rebootuser/LinEnum](https://github.com/rebootuser/LinEnum)[](https://github.com/rebootuser/LinEnum)
- **LES (Linux Exploit Suggester):** [https://github.com/mzet-/linux-exploit-suggester](https://github.com/mzet-/linux-exploit-suggester)

#### . ✅ **LinPEAS**

- **Why:** It’s the **most comprehensive and actively maintained** tool in the game.
    
- **Strengths:**
    
    - Automated, colored output.
        
    - Enumerates everything: SUIDs, capabilities, kernel exploits, services, credentials, Docker misconfigurations, etc.
        
    - Frequently updated with new checks.
        
- **Best Use:** Full enumeration when you’ve just landed a shell. It's your go-to one-shot recon.
    

#### 2. ✅ **LES (Linux Exploit Suggester)**

- **Why:** Perfect for quickly suggesting known exploits for kernel and software versions.
    
- **Strengths:**
    
    - Extremely fast.
        
    - Suggests **public exploits** based on kernel version and packages.
        
- **Best Use:** When you're ready to escalate and want to find known kernel exploits to weaponize.
    

#### 3. ✅ **LinEnum**

- **Why:** Lightweight and very effective for **manual enumeration**.
    
- **Strengths:**
    
    - Great balance between noise and info.
        
    - Easy to review manually.
        
    - Widely used in CTFs and real-world pentests.
        
- **Best Use:** As a complement to LinPEAS — for cross-referencing or when PEAS is too noisy.
    

---

### 🧠 Pro Tip:

Use **LinPEAS first** for full info, then **LinEnum or LES** to validate or exploit specific findings.

Let me know if you want a cheatsheet or example usage for each.


Here’s a well-structured Obsidian-friendly note covering the top Linux privilege escalation tools:

---

## 🧰 Linux Privilege Escalation Tools – Field-Tested Essentials

> 📁 **Category:** Privilege Escalation  
> 🏴‍☠️ **Usage:** Post-exploitation, enumeration  
> 🧠 **Goal:** Identify misconfigurations, known exploits, and privilege escalation vectors

---

### 🔥 1. `LinPEAS`

> **Repository:** [LinPEAS GitHub](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS)  
> **Author:** carlospolop  
> **Language:** Bash

#### ✅ Key Features:

- Full system enumeration.
    
- Identifies **SUID/SGID binaries**, **capabilities**, **kernel exploits**, **Docker/LXC misconfigurations**, **weak file perms**, **cron jobs**, **NFS issues**, etc.
    
- **Color-coded output** (easy to spot risks).
    
- Multiple modes: `linpeas.sh`, `linpeas_linux_amd64`, etc.
    

#### 📌 Usage:

```bash
curl -s https://raw.githubusercontent.com/carlospolop/privilege-escalation-awesome-scripts-suite/master/linPEAS/linpeas.sh | bash
```

Or upload locally:

```bash
chmod +x linpeas.sh && ./linpeas.sh
```

#### 🧠 Notes:

- Best used as **initial recon** after gaining a shell.
    
- Output is verbose → pipe to file or scroll slowly.
    
- Works great in combination with manual post-analysis.
    

---

### ⚔️ 2. `LES (Linux Exploit Suggester)`

> **Repository:** [LES GitHub](https://github.com/mzet-/linux-exploit-suggester)  
> **Author:** mzet  
> **Language:** Perl

#### ✅ Key Features:

- Detects **kernel version** and suggests known **public exploits**.
    
- Lightweight and fast.
    
- Maintained for older kernels.
    

#### 📌 Usage:

```bash
perl linux-exploit-suggester.pl
```

Or if downloaded:

```bash
chmod +x linux-exploit-suggester.pl
./linux-exploit-suggester.pl
```

#### 🧠 Notes:

- Great for **targeted escalation** via kernel vulnerabilities.
    
- Combine with `uname -a` and `lsb_release -a` output.
    
- Be cautious — most suggested exploits require compilation and may crash the system.
    

---

### 🛠️ 3. `LinEnum`

> **Repository:** [LinEnum GitHub](https://github.com/rebootuser/LinEnum)  
> **Author:** rebootuser  
> **Language:** Bash

#### ✅ Key Features:

- Manual-style enumeration of:
    
    - Users, groups, sudo perms
        
    - Cron jobs, services, processes
        
    - Network configs, files, environment variables
        
- Clean output (no color), grep-friendly.
    

#### 📌 Usage:

```bash
chmod +x LinEnum.sh
./LinEnum.sh -t -k password -r report.txt
```

Flags:

- `-t` → thorough checks
    
- `-k` → keyword search
    
- `-r` → write report to file
    

#### 🧠 Notes:

- Best for **manual triage** or CTFs.
    
- Use alongside LinPEAS for confirmation or comparison.
    
- Easy to grep for specific weaknesses.
    

---

### 📎 Honorable Mentions (Optional Use)

|Tool|Notes|
|---|---|
|`LSE` (Linux Smart Enumeration)|Minimal, stealthy, logic-based checks; good for noisy environments.|
|`linuxprivchecker.py`|Python-based, not actively maintained; okay for quick wins.|

---

### 💡 Best Practice Workflow:

```bash
1. Run LinPEAS for full discovery
2. Use LinEnum to confirm findings or for cleaner manual review
3. Use LES to check for kernel-specific public exploits
```

---

# Linked Notes
[[GTFOBins]]
[[Prive_Escal_App_Function]]
[[Types_PrivEsc(Linux)]]


