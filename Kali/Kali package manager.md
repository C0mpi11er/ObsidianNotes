Kali Linux is **Debian-based**, so package management is primarily handled by **APT (Advanced Package Tool)** along with **dpkg** for low-level package operations. Since you work extensively with Linux and offensive security tooling, the cheat sheet below focuses on **real operational workflows**, not just commands.

---

# Kali Linux Package Manager Cheat Sheet

## 1. Update & Upgrade Packages

|Command|Purpose|
|---|---|
|`sudo apt update`|Refresh package lists from repositories|
|`sudo apt upgrade`|Upgrade installed packages without removing dependencies|
|`sudo apt full-upgrade`|Upgrade packages including dependency changes|
|`sudo apt dist-upgrade`|Older equivalent of full-upgrade|
|`sudo apt update && sudo apt upgrade -y`|Update and upgrade in one command|

**Typical workflow**

```bash
sudo apt update
sudo apt full-upgrade -y
```

Used after:

- Fresh Kali install
    
- Adding repositories
    
- Before installing tools
    

---

## 2. Install Packages

|Command|Purpose|
|---|---|
|`sudo apt install <package>`|Install a package|
|`sudo apt install <pkg1> <pkg2>`|Install multiple packages|
|`sudo apt install -y <package>`|Install without confirmation|
|`sudo apt install --reinstall <package>`|Reinstall package|

Example:

```bash
sudo apt install nmap
sudo apt install gobuster wfuzz
```

---

## 3. Remove Packages

|Command|Purpose|
|---|---|
|`sudo apt remove <package>`|Remove package but keep config files|
|`sudo apt purge <package>`|Remove package and configuration files|
|`sudo apt autoremove`|Remove unused dependencies|
|`sudo apt autopurge`|Remove unused dependencies + configs|

Example:

```bash
sudo apt purge metasploit-framework
sudo apt autoremove
```

---

## 4. Search for Packages

|Command|Purpose|
|---|---|
|`apt search <keyword>`|Search repository packages|
|`apt-cache search <keyword>`|Legacy search|
|`apt show <package>`|Show package information|

Example:

```bash
apt search fuzz
apt show gobuster
```

---

## 5. List Installed Packages

|Command|Purpose|
|---|---|
|`apt list --installed`|List installed packages|
|`dpkg -l`|Detailed installed packages list|
|`apt list --upgradable`|Show packages that can be upgraded|

Example:

```bash
apt list --installed | grep nmap
```

---

## 6. Package Details

|Command|Purpose|
|---|---|
|`apt show <package>`|Package metadata|
|`apt-cache policy <package>`|Show repo version and installed version|
|`dpkg -s <package>`|Show package status|

Example:

```bash
apt-cache policy nmap
```

Shows:

- Installed version
    
- Repository version
    
- Priority
    

---

# 7. Fix Broken Packages

Common issue in Kali when installing many pentesting tools.

|Command|Purpose|
|---|---|
|`sudo apt --fix-broken install`|Fix dependency issues|
|`sudo dpkg --configure -a`|Finish interrupted installs|
|`sudo apt update --fix-missing`|Fix missing packages|

Typical recovery workflow:

```bash
sudo dpkg --configure -a
sudo apt --fix-broken install
sudo apt update
```

---

# 8. Download Packages Without Installing

Useful for **offline installs or analysis**.

```bash
apt download <package>
```

Example:

```bash
apt download nmap
```

Produces `.deb` file.

---

# 9. Install Local `.deb` Packages

```bash
sudo dpkg -i package.deb
```

Fix dependencies afterward:

```bash
sudo apt --fix-broken install
```

---

# 10. Clean Package Cache

APT stores downloaded packages in:

```
/var/cache/apt/archives/
```

Commands:

|Command|Purpose|
|---|---|
|`sudo apt clean`|Remove all cached packages|
|`sudo apt autoclean`|Remove obsolete cached packages|

Example:

```bash
sudo apt clean
```

---

# 11. Manage Repositories

Repository config:

```
/etc/apt/sources.list
```

or

```
/etc/apt/sources.list.d/
```

Add repo:

```bash
sudo nano /etc/apt/sources.list
```

Example Kali repo:

```
deb http://http.kali.org/kali kali-rolling main contrib non-free
```

Then refresh:

```bash
sudo apt update
```

---

# 12. Install Kali Tool Categories

Kali provides **metapackages**.

|Package|Installs|
|---|---|
|`kali-linux-default`|Standard Kali tools|
|`kali-linux-large`|More tools|
|`kali-linux-everything`|All Kali tools|
|`kali-tools-web`|Web pentesting tools|
|`kali-tools-wireless`|Wireless hacking tools|
|`kali-tools-exploitation`|Exploit tools|

Example:

```bash
sudo apt install kali-tools-web
```

---

# 13. Upgrade Kali Version (Rolling)

Kali is **rolling release**, so version upgrades are simple:

```bash
sudo apt update
sudo apt full-upgrade -y
```

Then reboot.

---

# 14. Hold Packages (Prevent Updates)

Useful when a tool breaks after update.

```bash
sudo apt-mark hold <package>
```

Remove hold:

```bash
sudo apt-mark unhold <package>
```

Example:

```bash
sudo apt-mark hold burpsuite
```

---

# 15. Find Which Package Installed a File

Very useful in Linux debugging.

```bash
dpkg -S <file>
```

Example:

```bash
dpkg -S /usr/bin/nmap
```

---

# 16. View Files Installed by a Package

```bash
dpkg -L <package>
```

Example:

```bash
dpkg -L gobuster
```

---

# 17. Check Package Dependencies

```bash
apt-cache depends <package>
```

Example:

```bash
apt-cache depends metasploit-framework
```

---

# 18. Check Reverse Dependencies

Shows packages that depend on it.

```bash
apt-cache rdepends <package>
```

---

# Quick Pentester Workflow (Real Kali Usage)

Typical tool setup:

```bash
sudo apt update
sudo apt full-upgrade -y

sudo apt install gobuster wfuzz feroxbuster sqlmap nuclei
```

Clean system:

```bash
sudo apt autoremove
sudo apt clean
```

Fix issues:

```bash
sudo dpkg --configure -a
sudo apt --fix-broken install
```

---

# Bonus: Apt vs Apt-get vs Dpkg

|Tool|Level|Usage|
|---|---|---|
|`apt`|High-level|Daily usage|
|`apt-get`|Advanced|Scripts / automation|
|`dpkg`|Low-level|Direct `.deb` management|

Example chain:

```
dpkg → apt-get → apt
```

---

If you want, I can also give you a **Kali package manager cheat sheet specifically for cybersecurity tool management** (like installing wordlists, SecLists, payload repos, etc.), which is **very useful for offensive security workflows.**