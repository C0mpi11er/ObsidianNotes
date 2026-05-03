
# 🧠 Cracking Protected Archives Cheat Sheet

---

> [!info] 🗂️ Common Archive Types
>
> ```bash
> .zip .rar .tar .gz .7z .gzip .kdbx .bitlocker .vhd
> ```
>
> 💡 Archives bundle multiple files → often password-protected

---

> [!tip] 🔎 Find Archive Files on System
>
> ```bash
> find / -type f \( -name "*.zip" -o -name "*.rar" -o -name "*.7z" \) 2>/dev/null
> ```
>
> 💡 Useful during post-exploitation

---

> [!info] 🌐 Get Full Archive Extension List (OSINT)
>
> ```bash
> curl -s https://fileinfo.com/filetypes/compressed | html2text | awk '{print tolower($1)}' | grep "\." | tee compressed_ext.txt
> ```
>
> 💡 Generates hundreds of archive extensions automatically

---

# 🗜️ ZIP File Cracking

---

> [!success] 📦 Extract Hash from ZIP
>
> ```bash
> zip2john archive.zip > zip.hash
> ```
>
> 💡 Converts ZIP → crackable hash

---

> [!tip] 🔓 Crack ZIP with John
>
> ```bash
> john --wordlist=rockyou.txt zip.hash
> ```
>
> ```bash
> john zip.hash --show
> ```
>
> 💡 Shows recovered passwords

---

> [!warning] ⚠️ Common Error (Important)
>
> ```bash
> Did not find End Of Central Directory
> ```
>
> 💡 Means:
>
> * ❌ File is NOT a ZIP
> * ❌ File is corrupted
> * ❌ Wrong extension (e.g. `.xlsx` ≠ zip sometimes)
>
> ✔ Fix:
>
> ```bash
> file filename
> ```

---

# 🧪 OpenSSL Encrypted GZIP Cracking

---

> [!info] 🔍 Identify Encryption Type
>
> ```bash
> file GZIP.gzip
> ```
>
> 💡 Example output:
>
> ```
> openssl enc'd data with salted password
> ```

---

> [!danger] 💥 Brute Force OpenSSL GZIP
>
> ```bash
> for i in $(cat rockyou.txt); do 
>   openssl enc -aes-256-cbc -d -in GZIP.gzip -k $i 2>/dev/null | tar xz
> done
> ```
>
> 💡 Extracts files ONLY if password is correct

---

> [!warning] ⚠️ Ignore Errors
>
> ```bash
> gzip: stdin: not in gzip format
> ```
>
> 💡 Normal during brute-force attempts

---

# 🔐 BitLocker Cracking

---

> [!info] 📥 Extract BitLocker Hash
>
> ```bash
> bitlocker2john -i Backup.vhd > backup.hashes
> ```
>
> ```bash
> grep "bitlocker\$0" backup.hashes > backup.hash
> ```
>
> 💡 Focus on `$bitlocker$0$` (password hash)

---

> [!success] ⚡ Crack with Hashcat
>
> ```bash
> hashcat -a 0 -m 22100 backup.hash rockyou.txt
> ```
>
> 💡 Hash mode:
>
> ```
> 22100 = BitLocker
> ```

---

> [!warning] 🐢 Performance Note
>
> 💡 BitLocker uses AES → VERY slow cracking
> → Requires strong GPU

---

# 💻 Mount BitLocker (Linux)

---

> [!info] 📦 Install Tool
>
> ```bash
> sudo apt-get install dislocker
> ```

---

> [!tip] 📁 Prepare Mount Points
>
> ```bash
> sudo mkdir -p /media/bitlocker
> sudo mkdir -p /media/bitlockermount
> ```

---

> [!success] 🔓 Decrypt & Mount
>
> ```bash
> sudo losetup -f -P Backup.vhd
> ```
>
> ```bash
> sudo dislocker /dev/loop0p2 -uPASSWORD -- /media/bitlocker
> ```
>
> ```bash
> sudo mount -o loop /media/bitlocker/dislocker-file /media/bitlockermount
> ```

---

> [!tip] 📂 Access Files
>
> ```bash
> cd /media/bitlockermount
> ls -la
> ```

---

> [!warning] ❌ Unmount After Use
>
> ```bash
> sudo umount /media/bitlockermount
> sudo umount /media/bitlocker
> ```

---

# ⚡ Real-World Workflow

---

> [!success] 🧠 Archive Attack Flow
>
> ```bash
> 1. Identify file type → file <file>
> 2. Extract hash → zip2john / bitlocker2john
> 3. Crack → john / hashcat
> 4. Access data → mount / extract
> ```

---

> [!danger] 💥 Common Mistakes
>
> * Using wrong tool (e.g. `zip2john` on `.xlsx`)
> * Ignoring file type check
> * Weak wordlists
> * Not using rules/mutations

---

# 🧠 Mental Model

---

> [!quote] 🎯 Think Like This
>
> ```bash
> ARCHIVE → HASH → WORDLIST → CRACK → ACCESS
> ```

---

> [!tip] 🚀 Pro Tips
>
> * Always run:
>
>   ```bash
>   file <target>
>   ```
> * Use custom wordlists (OSINT-based)
> * Combine with rules for better success
> * GPU = huge advantage for Hashcat
> * Don’t trust file extensions blindly

---
