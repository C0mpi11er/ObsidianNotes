Here’s an **enhanced John the Ripper Cheat Sheet** with **real-world, out-of-the-box commands** that you can copy and run immediately (assuming hash files are prepared and you have John and the relevant scripts/tools installed):

---

# 🧠 **John the Ripper Cheat Sheet with Out-of-the-Box Commands**

---

## 🏁 **Basic Wordlist Attack**

```bash
john --wordlist=/usr/share/john/password.lst hashes.txt
```

> 📌 Uses a default wordlist to crack hashes in `hashes.txt`.

---

## 💪 **Wordlist + Rules (Stronger)**

```bash
john --wordlist=/usr/share/john/password.lst --rules hashes.txt
```

> 📌 Tries mangled variations (e.g., Password1, p@ssword, etc.).

---

## 🔐 **Incremental (Brute-force)**

```bash
john --incremental hashes.txt
```

> 🔁 Tries all possible character combinations (slow but thorough).

---

## 🧩 **Mask Attack (Custom Brute-force)**

```bash
john --mask=?l?l?l?l?d?d hashes.txt
```

> 🎯 Attempts 4 lowercase letters + 2 digits (e.g., `word12`).

---

## 🧠 **Crack Office 2010+ Password (GPU)**

```bash
john --format=office-opencl officehash.txt
```

> 🚀 GPU cracking for MS Office docs (2010+).

---

## 📦 **Extract Hash from Office File**

```bash
office2john.py protected.docx > officehash.txt
```

---

## 📦 **Extract Hash from ZIP**

```bash
zip2john secret.zip > ziphash.txt
john ziphash.txt
```

---

## 🧾 **Extract Hash from RAR**

```bash
rar2john secret.rar > rarhash.txt
john rarhash.txt
```

---

## 🔏 **Extract Hash from PDF**

```bash
pdf2john.pl protected.pdf > pdfhash.txt
john pdfhash.txt
```

---

## 🧪 **Benchmark John**

```bash
john --test
john --test --format=office-opencl
```

> ⚙️ Test your CPU/GPU speed on various hash types.

---

## 🔎 **Show Cracked Passwords**

```bash
john --show hashes.txt
```

---

## 📋 **List Supported Hash Formats**

```bash
john --list=formats
```

---

## ⚙️ **List OpenCL GPU Devices**

```bash
john --list=opencl-devices
```

---

## ⏳ **Save and Restore Sessions**

```bash
john --session=mycrack --wordlist=rockyou.txt hashes.txt
# Later...
john --restore=mycrack
```

---

## 📈 **Monitor Status**

```bash
john --status=mycrack
```

---

## 📁 **Use Custom Wordlist**

```bash
john --wordlist=rockyou.txt hashes.txt
```

---

## 💣 **Try All at Once (Smart Combo)**

```bash
john --wordlist=rockyou.txt --rules --fork=4 hashes.txt
```

> 💡 Combines speed, rules, and multi-core CPU cracking.

---

Would you like me to compile this into a **PDF** or **Markdown** file for your toolkit?