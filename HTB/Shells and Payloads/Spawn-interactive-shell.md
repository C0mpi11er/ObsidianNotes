# Spawning Interactive Shells — Explained Simply

## 🎯 What’s the problem?

> [!NOTE]  
> After exploitation, you usually get a **weak / limited shell**

Example:

www-data@target$

### Why it’s bad:

> [!WARNING]  
> Weak shells:

- ❌ No proper prompt
- ❌ No tab completion
- ❌ `sudo`, `su` may fail
- ❌ Hard to navigate

---

## 🔥 Goal

> [!IMPORTANT]  
> Convert weak shell → **interactive (TTY) shell**

Weak shell → Interactive shell → Full usability

---

# 🛠️ Methods to Spawn Interactive Shells

## 🐚 1. Basic Shell (Quick Fix)

/bin/sh -i

> [!TIP]  
> `-i` = interactive mode

Output:

sh-4.2$

⚠️ Still limited, but better than nothing

---

## 🐍 2. Python (Best Option — if available)

python -c 'import pty; pty.spawn("/bin/sh")'

> [!SUCCESS]  
> Gives a **proper TTY shell**

---

## 🐪 3. Perl

perl -e 'exec "/bin/sh";'

> [!NOTE]  
> Executes `/bin/sh` using Perl

---

## 💎 4. Ruby

ruby -e 'exec "/bin/sh"'

---

## 🌙 5. Lua

lua -e 'os.execute("/bin/sh")'

---

## 🧮 6. AWK (Very common fallback)

awk 'BEGIN {system("/bin/sh")}'

> [!TIP]  
> AWK exists on most Linux systems → very reliable

---

## 🔍 7. Find Command Trick

### Method 1 (via awk):

find / -name test -exec /bin/awk 'BEGIN {system("/bin/sh")}' \;

### Method 2 (direct shell):

find . -exec /bin/sh \; -quit

> [!IMPORTANT]  
> `-exec` lets you run commands → abuse it to spawn shell

---

## 📝 8. Vim Escape (very niche but powerful)

### One-liner:

vim -c ':!/bin/sh'

### Inside vim:

:set shell=/bin/sh  
:shell

> [!WARNING]  
> Works only if you can open vim

---

# 🧠 Key Concept

> [!IMPORTANT]  
> `/bin/sh` and `/bin/bash` are just **shell binaries**

You can replace them with:

/bin/bash  
/bin/zsh  
/bin/dash

---

# 🔐 Permissions Matter

## 📂 Check file permissions

ls -la <file>

> [!TIP]  
> Look for:

- `rwx` (read/write/execute)
- SUID binaries
- writable scripts

---

## 🔑 Check sudo privileges

sudo -l

Example:

User apache may run the following commands:  
(ALL : ALL) NOPASSWD: ALL

> [!SUCCESS]  
> This means → **you can run ANY command as root**

---

## ⚠️ Important Detail

> [!WARNING]  
> `sudo -l` needs a **stable interactive shell**

If your shell is weak:

- It may return nothing
- Or behave weird

---

# 🧠 Big Picture (VERY IMPORTANT)

> [!SUCCESS]  
> Real attack flow:

1. Exploit → get shell
2. Shell is weak 😒
3. Upgrade shell (TTY) 🔥
4. Check permissions
5. Escalate privileges

---

# ⚡ Quick Memory Cheat

No Python? → Try:  
sh → perl → ruby → awk → find → vim

---

# 🔥 Pro Insight (what separates beginners)

> [!TIP]  
> Always try **multiple shell upgrade methods**

Because:

- Some binaries won’t exist
- Some commands are restricted
- Some shells are jailed