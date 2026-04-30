
# 🧠 MSFconsole `search` Cheat Sheet

---

> [!info] 🧩 Basic Syntax
> 
> ```bash
> search <keyword>
> ```
> 
> **Examples**
> 
> ```bash
> search smb
> search apache
> search wordpress
> ```

---

> [!tip] 🎯 Search by Module Type
> 
> ```bash
> search type:exploit
> search type:auxiliary
> search type:post
> search type:payload
> search type:encoder
> search type:evasion
> ```
> 
> **Example**
> 
> ```bash
> search smb type:exploit
> ```

---

> [!danger] 🧨 Search by Vulnerability (CVE / EDB / BID)
> 
> ```bash
> search cve:2021
> search cve:2017-0144
> search edb:12345
> search bid:12345
> ```
> 
> 💡 Map scan results → working exploits

---

> [!tip] 🖥️ Filter by Platform / Architecture
> 
> ```bash
> search platform:windows
> search platform:linux
> search arch:x64
> search arch:x86
> ```
> 
> **Example**
> 
> ```bash
> search smb type:exploit platform:windows
> ```

---

> [!info] 🚪 Search by Port
> 
> ```bash
> search port:445
> search port:80
> search port:21
> ```
> 
> 💡 Perfect after Nmap scans

---

> [!success] ⚡ Filter by Rank (VERY IMPORTANT)
> 
> ```bash
> search rank:excellent
> search rank:great
> search rank:good
> search rank:gte400
> ```
> 
> 💡 Focus on reliable exploits only

---

> [!tip] 🧪 Only Modules with `check` Support
> 
> ```bash
> search check:true
> ```
> 
> 💡 Safer exploitation (verify before exploit)

---

> [!danger] 🔥 Combine Filters (REAL POWER)
> 
> ```bash
> search type:exploit platform:windows port:445
> ```
> 
> ```bash
> search cve:2017 type:exploit platform:windows
> ```
> 
> ```bash
> search type:auxiliary port:80 name:scanner
> ```
> 
> 💡 This is how real pentesters search

---

> [!warning] ❌ Exclude Results
> 
> ```bash
> search type:exploit platform:-linux
> search smb -scanner
> ```
> 
> 💡 Use `-` to remove noise

---

> [!info] 📊 Sorting Results
> 
> ```bash
> search smb -s rank
> search smb -s date
> search smb -s name
> ```
> 
> 🔽 Reverse Order
> 
> ```bash
> search smb -s rank -r
> ```

---

> [!tip] ⚡ Auto-Use Module (Lazy Mode)
> 
> ```bash
> search eternalblue -u
> ```
> 
> 💡 Auto loads if only one result

---

> [!info] 📁 Save Output to File
> 
> ```bash
> search smb -o smb_results.csv
> ```

---

> [!tip] 🧪 Regex Filtering
> 
> ```bash
> search smb -S exploit
> ```
> 
> 💡 Filter results using regex

---

> [!info] 🧠 MITRE ATT&CK Mapping
> 
> ```bash
> search att&ck:T1059
> ```
> 
> 💡 Useful for reporting + mapping techniques

---

# ⚡ Real-World Workflows

---

> [!success] 🧨 Nmap → Exploit
> 
> ```bash
> nmap -p 445 target
> ```
> 
> ```bash
> search port:445 type:exploit platform:windows
> ```

---

> [!danger] 💥 EternalBlue Example
> 
> ```bash
> search eternalblue
> search cve:2017-0144
> ```

---

> [!tip] 🌐 Web Exploitation
> 
> ```bash
> search type:auxiliary port:80
> search wordpress
> search apache
> ```

---

> [!tip] 🧬 Post Exploitation
> 
> ```bash
> search type:post platform:windows
> search session_type:meterpreter
> ```

---

# 🧠 Mental Model

> [!quote] 🎯 Think Like This
> 
> ```bash
> search WHAT + WHERE + HOW_GOOD
> ```
> 
> **Example**
> 
> ```bash
> search smb type:exploit platform:windows rank:excellent
> ```

---

> [!warning] 🚨 Pro Tips
> 
> - Always combine filters → reduces noise
>     
> - Always sort by rank:
>     
>     ```bash
>     -s rank
>     ```
>     
> - Prefer:
>     
>     ```bash
>     type:exploit + platform + rank
>     ```
>     
> - Use `check:true` before exploiting
>     

---
