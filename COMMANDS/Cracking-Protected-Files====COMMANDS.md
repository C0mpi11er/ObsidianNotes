Alright — this is the **exact structure + style** you want. Clean, consistent, Obsidian-style callouts. No shortcuts.

---

# 🔐 Cracking Protected Files Cheat Sheet

---

> [!info] 🧩 Hunting for Encrypted Files
>
> ```bash
> for ext in $(echo ".xls .xls* .xltx .od* .doc .doc* .pdf .pot .pot* .pp*"); do 
>   echo -e "\nFile extension: " $ext
>   find / -name *$ext 2>/dev/null | grep -v "lib\|fonts\|share\|core"
> done
> ```
>
> 💡 Quickly locates **documents likely to be encrypted or sensitive**

---

> [!tip] 🔑 Hunting for SSH Private Keys
>
> ```bash
> grep -rnE '^\-{5}BEGIN [A-Z0-9]+ PRIVATE KEY\-{5}$' /* 2>/dev/null
> ```
>
> 💡 SSH keys often **have no extension**, so search by content

---

> [!info] 🔍 Check if SSH Key is Encrypted
>
> ```bash
> ssh-keygen -yf ~/.ssh/id_rsa
> ```
>
> * No prompt → ✅ Not encrypted
> * Prompt → 🔒 Encrypted

---

> [!danger] 🔄 Convert Files → Crackable Hash (REQUIRED)
>
> ```bash
> ssh2john.py SSH.private > ssh.hash
> ```
>
> ```bash
> office2john.py Protected.docx > protected.hash
> ```
>
> ```bash
> pdf2john.py file.pdf > pdf.hash
> ```
>
> 💡 You **cannot crack files directly** — must extract hashes first

---

> [!tip] 🔎 Find Available Converters
>
> ```bash
> locate *2john*
> ```
>
> 💡 Shows all supported formats (zip, rar, 7z, keepass, etc.)

---

> [!success] 💥 Crack Extracted Hash (John the Ripper)
>
> ```bash
> john --wordlist=rockyou.txt ssh.hash
> ```
>
> ```bash
> john --wordlist=rockyou.txt protected.hash
> ```
>
> ```bash
> john --wordlist=rockyou.txt pdf.hash
> ```
>
> 💡 Same workflow for **all file types**

---

> [!info] 👀 Show Cracked Passwords
>
> ```bash
> john ssh.hash --show
> ```
>
> 💡 Displays recovered passwords from `.pot` file

---

> [!danger] ⚡ Full Workflow (REAL ATTACK FLOW)
>
> ```bash
> ssh2john.py SSH.private > ssh.hash
> ```
>
> ```bash
> john --wordlist=rockyou.txt ssh.hash
> ```
>
> ```bash
> john ssh.hash --show
> ```
>
> 💡 Extract → Crack → Reveal

---

> [!tip] 🧰 Common `2john` Scripts
>
> ```bash
> ssh2john.py        # SSH keys
> office2john.py     # Office docs
> pdf2john.py        # PDFs
> zip2john           # ZIP files
> rar2john           # RAR files
> 7z2john.pl         # 7zip files
> ```
>
> 💡 These cover **most real-world scenarios**

---

> [!warning] ⚠️ Modern Password Reality
>
> * ≥12 characters
> * Upper + Lower + Number + Symbol
>
> 💡 Basic wordlists = ❌ not enough
> Use **custom + mutated lists**

---

> [!tip] 🚀 Improve Success Rate
>
> ```bash
> john --wordlist=custom.txt --rules ssh.hash
> ```
>
> 💡 Rules = mutate words → **more candidates**

---

# ⚡ Real-World Workflows

---

> [!success] 🧨 Crack SSH Private Key
>
> ```bash
> ssh2john.py id_rsa > ssh.hash
> ```
>
> ```bash
> john --wordlist=rockyou.txt ssh.hash
> ```

---

> [!danger] 💥 Crack Protected Office File
>
> ```bash
> office2john.py file.docx > hash.txt
> ```
>
> ```bash
> john --wordlist=rockyou.txt hash.txt
> ```

---

> [!tip] 📄 Crack PDF File
>
> ```bash
> pdf2john.py file.pdf > pdf.hash
> ```
>
> ```bash
> john --wordlist=rockyou.txt pdf.hash
> ```

---

# 🧠 Mental Model

---

> [!quote] 🎯 Think Like This
>
> ```bash
> FILE → HASH → WORDLIST → RULES → CRACK
> ```

---

> [!warning] 🚨 Pro Tips
>
> * Always extract hash first
> * Always use custom wordlists
> * Always try rules:
>
> ```bash
> --rules
> ```
>
> * Combine tools:
>
>   * `cupp` → OSINT list
>   * `crunch` → structured list
>   * `hashcat` → mutation
>   * `john` → cracking

---

2