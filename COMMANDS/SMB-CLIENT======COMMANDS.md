# 🧠 `smbclient` Cheat Sheet

---

> [!info] 🧩 Basic Syntax

```bash
smbclient //TARGET/SHARE -U USER
```

**Example**

```bash
smbclient //10.10.10.10/SHARE -U admin
```

---

> [!tip] 🔍 List Shares on a Host

```bash
smbclient -L //TARGET -U USER
```

**Anonymous (no password)**

```bash
smbclient -L //10.10.10.10 -N
```

---

> [!danger] 🔐 Connect to a Share

```bash
smbclient //TARGET/SHARE -U USER
```

**Example**

```bash
smbclient //10.10.10.10/public -U admin
```

---

> [!tip] 🚫 Anonymous Login

```bash
-N
```

**Example**

```bash
smbclient //10.10.10.10/public -N
```

---

> [!tip] 🔑 Password Inline

```bash
smbclient //TARGET/SHARE -U USER%PASSWORD
```

**Example**

```bash
smbclient //10.10.10.10/public -U admin%password123
```

---

> [!tip] 📂 Change Directory (inside SMB session)

```bash
cd folder
ls
```

---

> [!tip] 📥 Download File

```bash
get filename
```

**Example**

```bash
get secret.txt
```

---

> [!tip] 📤 Upload File

```bash
put file.txt
```

---

> [!danger] 📁 Recursive Download (ALL FILES)

```bash
recurse ON
prompt OFF
mget *
```

---

> [!info] 🧭 List Files in Share

```bash
ls
dir
```

---

> [!warning] ⚙️ Connect with Specific Port

```bash
-p PORT
```

**Example**

```bash
smbclient //10.10.10.10/share -U admin -p 445
```

---

> [!info] 🧪 Run Single Command (No Interactive Shell)

```bash
-c "command"
```

**Example**

```bash
smbclient //10.10.10.10/share -U admin -c "ls"
```

---

> [!tip] 📊 Debug / Verbose Mode

```bash
-d LEVEL
```

**Example**

```bash
smbclient -d 3 //10.10.10.10/share -U admin
```

---

> [!danger] 🔐 NTLM Hash Authentication

```bash
--pw-nt-hash
```

**Example**

```bash
smbclient //10.10.10.10/share -U admin --pw-nt-hash
```

---

> [!info] 🌐 Specify Domain

```bash
-W DOMAIN
```

**Example**

```bash
smbclient //10.10.10.10/share -U admin -W CORP
```

---

> [!tip] ⚡ Quick Recon Workflow

```bash
smbclient -L //TARGET -N
smbclient //TARGET/SHARE -N
ls
get file.txt
```

---

> [!warning] 🧠 Pro Tips

* Always start with `-L` (enumeration first)
* Try `-N` before credentials
* Use `recurse ON` for full share dump
* Combine with `netexec smb --shares` for faster recon

---


