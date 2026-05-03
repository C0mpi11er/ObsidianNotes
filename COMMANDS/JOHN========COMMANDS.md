
---

> [!info] 🚀 Basic Usage
>
> ```
> john [OPTIONS] [PASSWORD-FILES]
> ```
>
> * Input = hash file
> * Output = cracked passwords stored in `.pot` file

---

> [!tip] 🔑 Core Cracking Modes
> These are the **most important commands you’ll use 90% of the time**:

> [!example] Single Crack Mode (targeted)
>
> ```
> john --single <hash_file>
> ```

> [!example] Wordlist Mode (dictionary)
>
> ```
> john --wordlist=rockyou.txt <hash_file>
> ```

> [!example] Incremental Mode (brute-force)
>
> ```
> john --incremental <hash_file>
> ```

> [!example] Mask Attack (pattern-based)
>
> ```
> john --mask='?l?l?l?d?d' <hash_file>
> ```

---

> [!important] 📖 Wordlist & Rules (VERY POWERFUL)
>
> ```
> john --wordlist=<file> --rules <hash_file>
> ```
>
> * Applies transformations:
>
>   * `password → Password1!`
> * Combine multiple:
>
> ```
> john --wordlist=a.txt,b.txt --rules <hash_file>
> ```

> [!tip] Advanced Rule Usage
>
> ```
> --rules=SECTION
> --rules=:rule
> --rules-stack=SECTION
> ```

---

> [!tip] 🧠 Single Mode Advanced Options
>
> ```
> --single-seed=WORD
> --single-wordlist=FILE
> --single-user-seed=FILE
> ```
>
> * Adds **extra candidate seeds**
> * Uses **user-specific intelligence**

---

> [!warning] 💥 Incremental (Brute Force Tuning)
>
> ```
> john --incremental=ASCII <hash_file>
> ```
>
> Customize:
>
> ```
> --min-length=N
> --max-length=N
> --incremental-charcount=N
> ```

> [!danger] ⚠️ Note
>
> * Very **slow & resource-heavy**
> * Use as **last resort**

---

> [!tip] 🎯 Mask & Smart Attacks
>
> ```
> john --mask='?u?l?l?l?d?d' <hash_file>
> ```
>
> Common mask chars:
>
> * `?l` = lowercase
> * `?u` = uppercase
> * `?d` = digit
> * `?s` = symbol

✔ Faster than brute-force when pattern is known

---

> [!info] 🧬 Advanced Modes (Less Common but Powerful)

> [!example] Markov Mode (probability-based)
>
> ```
> john --markov <hash_file>
> ```

> [!example] PRINCE Mode (word chaining)
>
> ```
> john --prince=wordlist.txt <hash_file>
> ```

> [!example] External Mode
>
> ```
> john --external=MODE <hash_file>
> ```

---

> [!important] 🔍 Hash Format Control
>
> ```
> john --format=raw-md5 <hash_file>
> ```
>
> * Forces hash type
> * Useful when auto-detection fails

---

> [!tip] 📂 Session Management (VERY USEFUL)

> [!example] Name a session
>
> ```
> john --session=crack1 <hash_file>
> ```

> [!example] Resume session
>
> ```
> john --restore=crack1
> ```

> [!example] Check status
>
> ```
> john --status
> ```

---

> [!info] 📊 Output & Results

> [!example] Show cracked passwords
>
> ```
> john --show <hash_file>
> ```

> [!example] Show uncracked only
>
> ```
> john --show=left <hash_file>
> ```

---

> [!tip] ⚡ Performance & Scaling

> [!example] Use multiple CPUs
>
> ```
> john --fork=4 <hash_file>
> ```

> [!example] Limit runtime
>
> ```
> john --max-run-time=3600 <hash_file>
> ```

> [!example] Limit attempts
>
> ```
> john --max-candidates=1000000 <hash_file>
> ```

---

> [!info] 🧪 Testing & Benchmarking

> [!example] Benchmark system
>
> ```
> john --test
> ```

> [!example] Stress test
>
> ```
> john --stress-test
> ```

---

> [!tip] 📁 File / Hash Handling

> [!example] Use custom pot file
>
> ```
> john --pot=my.pot <hash_file>
> ```

> [!example] Change config
>
> ```
> john --config=custom.conf
> ```

---

> [!important] 👥 Filtering Targets
>
> ```
> --users=user1,user2
> --groups=1000
> --shells=/bin/bash
> ```

✔ Useful when cracking `/etc/shadow`

---

> [!summary] 🧠 Quick Workflow (HTB Style)
>
> 1. Identify hash format
> 2. Try:
>
>    ```
>    john --wordlist=rockyou.txt --rules hash.txt
>    ```
> 3. Then:
>
>    ```
>    john --single hash.txt
>    ```
> 4. Then:
>
>    ```
>    john --incremental hash.txt
>    ```
> 5. Check results:
>
>    ```
>    john --show hash.txt
>    ```

---

