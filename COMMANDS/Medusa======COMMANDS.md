
---

# 🧠 Medusa Command Cheat Sheet

---

> [!info] 🧩 Basic Syntax
>
> ```bash
> medusa -h <TARGET> -u <USER> -p <PASS> -M <MODULE>
> ```
>
> **Example**
>
> ```bash
> medusa -h 10.10.10.10 -u admin -p password -M ssh
> ```

---

> [!tip] 📂 Username & Password Lists (Most Common)
>
> ```bash
> medusa -h <TARGET> -U users.txt -P passwords.txt -M <MODULE>
> ```
>
> **Example**
>
> ```bash
> medusa -h 10.10.10.10 -U users.txt -P passwords.txt -M smbnt
> ```

---

> [!info] 🧾 Combo File (username:password)
>
> ```bash
> medusa -h <TARGET> -C combo.txt -M <MODULE>
> ```
>
> **Example**
>
> ```bash
> medusa -h 10.10.10.10 -C creds.txt -M ssh
> ```

---

> [!tip] 🔑 Try Empty & Username-as-Password
>
> ```bash
> medusa -h <TARGET> -U users.txt -e ns -M <MODULE>
> ```
>
> 💡 `-e` options:
>
> * `n` → empty password
> * `s` → password = username
> * `ns` → both

---

> [!info] 🌐 Multiple Targets
>
> ```bash
> medusa -H targets.txt -U users.txt -P passwords.txt -M <MODULE>
> ```

---

> [!tip] ⚡ Set Threads (Speed Control)
>
> ```bash
> medusa -h <TARGET> -U users.txt -P passwords.txt -M <MODULE> -t 4
> ```
>
> 💡 Recommended:
>
> * SSH → `-t 4–8`
> * SMB → `-t 1–2`
> * RDP → `-t 1–2`

---

> [!info] 🚫 Stop on First Success
>
> ```bash
> medusa -h <TARGET> -U users.txt -P passwords.txt -M <MODULE> -f
> ```
>
> * `-f` → stop per host
> * `-F` → stop globally

---

> [!tip] 📁 Save Results to File
>
> ```bash
> medusa -h <TARGET> -U users.txt -P passwords.txt -M <MODULE> -O results.txt
> ```

---

> [!info] 🔢 Custom Port
>
> ```bash
> medusa -h <TARGET> -U users.txt -P passwords.txt -M <MODULE> -n <PORT>
> ```
>
> **Example**
>
> ```bash
> medusa -h 10.10.10.10 -U users.txt -P passwords.txt -M ssh -n 2222
> ```

---

> [!tip] 🔒 Enable SSL
>
> ```bash
> medusa -h <TARGET> -U users.txt -P passwords.txt -M <MODULE> -s
> ```

---

> [!info] 🔍 Verbose Output
>
> ```bash
> medusa -h <TARGET> -U users.txt -P passwords.txt -M <MODULE> -v 4
> ```

---

> [!tip] 🧠 View Available Modules
>
> ```bash
> medusa -d
> ```

---

> [!info] ⚙️ Module-Specific Options
>
> ```bash
> medusa -M <MODULE> -q
> ```
>
> 👉 Example (HTTP login path):
>
> ```bash
> medusa -h 10.10.10.10 -U users.txt -P passwords.txt -M http -m DIR:/login
> ```

---

> [!tip] 🔥 Common Service Attacks
>
> **SSH**
>
> ```bash
> medusa -h 10.10.10.10 -U users.txt -P passwords.txt -M ssh -t 4
> ```
>
> **RDP**
>
> ```bash
> medusa -h 10.10.10.10 -U users.txt -P passwords.txt -M rdp -t 2
> ```
>
> **SMB**
>
> ```bash
> medusa -h 10.10.10.10 -U users.txt -P passwords.txt -M smbnt -t 2
> ```
>
> **FTP**
>
> ```bash
> medusa -h 10.10.10.10 -U users.txt -P passwords.txt -M ftp
> ```

---

# 🧠 Mental Model

> [!quote] 🎯 Think Like This
>
> ```text
> Target → Service → Users → Passwords → Threads → Results
> ```
>
> Typical flow:
>
> ```bash
> nmap → find service → medusa → valid creds → next tool (netexec / impacket)
> ```

---

