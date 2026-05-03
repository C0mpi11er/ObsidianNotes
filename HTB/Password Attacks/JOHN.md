
---

> [!info] 🔧 John the Ripper (JtR)
> **John the Ripper** is a popular **open-source password cracking tool** (released 1996, UNIX-based).
>
> * Supports **brute-force, dictionary, and rule-based attacks**
> * “**Jumbo version**” recommended → better performance, more features, multilingual wordlists, 64-bit support
> * Includes tools to **convert hashes/files into crackable formats**
> * Regularly updated for modern security trends

---

> [!tip] ⚙️ Cracking Modes Overview
> JtR has **three main cracking modes**:
>
> * Single crack mode (rule-based, targeted)
> * Wordlist mode (dictionary attack)
> * Incremental mode (advanced brute-force)

---

> [!important] 🧠 Single Crack Mode
> Rule-based attack using **user information**:
>
> * Username
> * Real name (GECOS fields)
> * Home directory
>
> Generates password guesses using **common transformations** (e.g. `Smith → Smith1`)

> [!example] Example Use
>
> ```
> john --single passwd
> ```
>
> * Extracts info like: `r0lf`, `Rolf Sebastian`, `/home/r0lf`
> * Applies rules → generates likely passwords
> * Efficient for **Linux credentials**

---

> [!tip] 📖 Wordlist Mode
> Performs a **dictionary attack** using a wordlist.

> [!example] Syntax
>
> ```
> john --wordlist=<wordlist_file> <hash_file>
> ```

> [!info] Key Details
>
> * Wordlist = **plain text (one password per line)**
> * Can use **multiple wordlists**
> * Supports **rules (`--rules`)**:
>
>   * Add numbers
>   * Capitalize letters
>   * Add special characters

✔ Most **practical attack in real-world pentesting**

---

> [!warning] 💥 Incremental Mode
> Advanced **brute-force-style attack using Markov chains** (statistical model)

> [!example] Syntax
>
> ```
> john --incremental <hash_file>
> ```

> [!info] Key Details
>
> * Generates passwords dynamically (no wordlist)
> * Tests **all combinations**, prioritizing likely ones
> * Uses config file: `john.conf`
> * Supports **custom charsets & lengths**

> [!danger] ⚠️ Limitation
>
> * Very **resource-intensive and slow**
> * Best used as **last resort**

---

> [!important] 🔍 Identifying Hash Formats
> Hashes may be **unknown**, JtR may not auto-detect.

> [!tip] Tools
>
> * JtR sample hashes
> * PentestMonkey list
> * **hashID tool**

> [!example] Example
>
> ```
> hashid -j <hash>
> ```
>
> * Suggests possible formats
> * Shows corresponding JtR format

> [!warning] Reality
>
> * Multiple matches possible
> * Must rely on **context (source of hash)**
> * Example: hash could be **RIPEMD-128**

---

> [!info] ⚙️ Hash Format Specification
> JtR supports **hundreds of formats**
> Use:
>
> ```
> john --format=<format> <hash_file>
> ```

> [!example] Examples
>
> * `raw-md5` → MD5
> * `nt` → Windows NTLM
> * `sha512crypt` → Linux SHA-512
> * `zip` → ZIP archive passwords

---

> [!tip] 📂 Cracking Files (2john Tools)
> JtR can crack **encrypted/password-protected files**

> [!example] General Syntax
>
> ```
> <tool> <file> > file.hash
> john file.hash
> ```

> [!info] Common Tools
>
> * `zip2john` → ZIP files
> * `rar2john` → RAR archives
> * `ssh2john` → SSH keys
> * `pdf2john` → PDFs
> * `office2john` → MS Office
> * `keepass2john` → KeePass DB

> [!success] ✔ Key Idea
> Convert file → **extract hash → crack with JtR**

---

> [!summary] 🧠 Key Takeaways
>
> * JtR is a **powerful, flexible cracking tool**
> * Choose mode based on situation:
>
>   * **Single** → targeted (user info)
>   * **Wordlist** → fastest practical attack
>   * **Incremental** → exhaustive, slow
> * Correct **hash format identification is critical**
> * Use **2john tools** to crack files

---

