Absolutely! Here's the **ultimate, enhanced John the Ripper Cheat Sheet**, combining **core usage**, **real-world commands**, and **out-of-the-box examples** into one powerful, ready-to-run reference.

---

# 🧠 John the Ripper Ultimate Cheat Sheet (2025 Edition)

---

## 🏁 Basic Cracking Modes

### 🔹 Wordlist Attack

```bash
john --wordlist=/usr/share/john/password.lst hashes.txt
```

> Uses a default wordlist to crack hashes in `hashes.txt`.

### 🔹 Wordlist + Rules (Stronger)

```bash
john --wordlist=/usr/share/john/password.lst --rules hashes.txt
```

> Applies mangling rules to create variants like `Password1`, `p@ssw0rd`.

### 🔹 Wordlist + Rules + Fork (Multithread)

```bash
john --wordlist=rockyou.txt --rules --fork=4 hashes.txt
```

> Runs rule-based cracking on 4 CPU cores.

### 🔹 Brute-force (Incremental)

```bash
john --incremental hashes.txt
```

> Pure brute-force using built-in charsets (slow but complete).

### 🔹 Mask Attack (Pattern Matching)

```bash
john --mask=?l?l?l?l?d?d hashes.txt
```

> E.g., cracks like `pass12`, `love99`.

---

## 🔐 Format-Specific Cracking

### 🔹 NTLM

```bash
john --format=nt hashes.txt
```

### 🔹 bcrypt

```bash
john --format=bcrypt hashes.txt
```

### 🔹 SHA-512 Crypt (Linux)

```bash
john --format=sha512crypt hashes.txt
```

### 🔹 Office 2010+ (GPU)

```bash
john --format=office-opencl officehash.txt
```

### 🔹 ZIP Archive

```bash
zip2john secret.zip > ziphash.txt
john ziphash.txt
```

### 🔹 RAR Archive

```bash
rar2john secret.rar > rarhash.txt
john rarhash.txt
```

### 🔹 PDF File

```bash
pdf2john.pl protected.pdf > pdfhash.txt
john pdfhash.txt
```

---

## 🧰 Hash Extraction Tools

|Format|Command|
|---|---|
|Office|`office2john.py file.docx > hash.txt`|
|ZIP|`zip2john file.zip > hash.txt`|
|RAR|`rar2john file.rar > hash.txt`|
|PDF|`pdf2john.pl file.pdf > hash.txt`|

---

## 💻 GPU / Performance Commands

### 🔹 Run Benchmark

```bash
john --test
john --test --format=office-opencl
```

### 🔹 List Supported Formats

```bash
john --list=formats
```

### 🔹 List GPU Devices (OpenCL)

```bash
john --list=opencl-devices
```

---

## 🔍 Monitoring, Session, and Output

### 🔹 Show Cracked Passwords

```bash
john --show hashes.txt
```

### 🔹 Monitor Cracking Progress

```bash
john --status
```

### 🔹 Save and Resume Sessions

```bash
john --session=mycrack --wordlist=rockyou.txt hashes.txt
# Later
john --restore=mycrack
```

---

## 🧠 Other Useful Flags

|Flag|Description|
|---|---|
|`--format=auto`|Autodetect hash type|
|`--pot=FILE`|Specify custom potfile|
|`--fork=N`|Parallelize using N processes|
|`--mask=?d?d?d?d`|Custom brute mask (4 digits)|
|`--rules=RULESET`|Use specific rule set|
|`--incremental=MODE`|Use specific charset (e.g., `ascii`, `digits`)|
|`--wordlist=FILE` or `-w=FILE`|Use a specific wordlist|
|`--stdout`|Output generated candidates without cracking|
|`--list=rules`|Show available rule sets|
|`--list=encodings`|Show charset encodings|
|`--nolog`|Disable logging to `john.log`|
|`--max-run-time=N`|Run for N seconds then stop|

---

## 🛠️ Installation Tips

### 💻 Community Version

```bash
sudo apt install john
```

### 🧰 Jumbo Version (full features)

```bash
git clone https://github.com/openwall/john.git
cd john/src
./configure && make -sj$(nproc)
```

---

## 🧪 Real-World Use Cases

### Crack NTLM from hashdump:

```bash
john --format=nt hashdump.txt
```

### Crack bcrypt with wordlist + rules:

```bash
john --format=bcrypt --wordlist=rockyou.txt --rules bcrypts.txt
```

### GPU crack Office 2016 password:

```bash
office2john.py encrypted.docx > officehash.txt
john --format=office-opencl officehash.txt
```

---

Would you like this exported as:

- ✅ **PDF**
    
- ✅ **Markdown**
    
- ✅ **One-page terminal-friendly text file**
    

Let me know your preferred format!