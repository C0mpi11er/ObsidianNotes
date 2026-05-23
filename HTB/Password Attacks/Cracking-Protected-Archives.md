

---

> [!info] 🧠 What are Protected Archives?
> 
> ```bash
> Compressed files (ZIP, RAR, 7z, GZIP, BitLocker volumes, etc.) that are secured using passwords or encryption.
> ```
> 
> 💡 Key Idea:
> 
> - You are not “breaking encryption”
>     
> - You are **recovering weak passwords protecting strong encryption**
>     
> - Work is done **offline**, not against a live service
>     
> 
> ✔ Think of it as **extracting a lock’s key model, then brute-forcing the key — not picking the lock itself**

---

# 🔁 General Attack Workflow

---

|Step|Action|
|:--|:--|
|1|Identify archive type|
|2|Extract hash or encrypted structure|
|3|Convert into crackable format|
|4|Run offline password attack|
|5|Decrypt / mount if successful|

---

# 🧪 File Identification

---

> [!success] 🔍 Identify File Type
> 
> ```bash
> file <archive>
> ```
> 
> 💡 Purpose:
> 
> - Detect real format (not just extension)
>     
> - Identify encryption type (OpenSSL, ZIP, BitLocker, etc.)
>     

---

# 📦 ZIP File Cracking

---

> [!success] 🗜️ Extract ZIP Hash
> 
> ```bash
> zip2john ZIP.zip > zip.hash
> cat zip.hash
> ```
> 
> 💡 What happens:
> 
> - Converts ZIP encryption metadata into a hash format readable by John the Ripper
>     

---

> [!success] 🔓 Crack ZIP Password
> 
> ```bash
> john --wordlist=/usr/share/wordlists/rockyou.txt zip.hash
> john zip.hash --show
> ```
> 
> 💡 Attack type:
> 
> - Dictionary attack (most effective in real-world cases)
>     

---

# 🔐 BitLocker Cracking

---

> [!warning] 💾 Extract BitLocker Hash
> 
> ```bash
> bitlocker2john -i Backup.vhd > backup.hashes
> grep "bitlocker\$0" backup.hashes > backup.hash
> ```
> 
> 💡 What you are extracting:
> 
> - Password-derived key material (not full disk encryption)
>     

---

> [!danger] ⚙️ Crack with Hashcat
> 
> ```bash
> hashcat -a 0 -m 22100 backup.hash /usr/share/wordlists/rockyou.txt
> ```
> 
> 💡 Notes:
> 
> - AES encryption is NOT being broken
>     
> - Only weak passwords are being guessed
>     

---

# 📂 OpenSSL Encrypted Files

---

> [!info] 🔎 Identify OpenSSL Encryption
> 
> ```bash
> file GZIP.gzip
> ```
> 
> ```bash
> openssl enc -aes-256-cbc -d -in GZIP.gzip -k <password>
> ```
> 
> 💡 Key Idea:
> 
> - File looks like gzip but is actually OpenSSL encrypted data
>     

---

> [!warning] 🔁 Brute Force Loop
> 
> ```bash
> for i in $(cat rockyou.txt); do
>   openssl enc -aes-256-cbc -d -in GZIP.gzip -k $i 2>/dev/null | tar xz
> done
> ```
> 
> 💡 Behavior:
> 
> - Tries passwords until decompression succeeds
>     
> - Errors are expected and ignored
>     

---

# 🧠 BitLocker Mounting (Linux)

---

> [!success] 🧩 Mount Process
> 
> ```bash
> sudo apt-get install dislocker
> sudo mkdir -p /media/bitlocker /media/bitlockermount
> ```
> 
> ```bash
> sudo losetup -f -P Backup.vhd
> sudo dislocker /dev/loop0p2 -u<PASSWORD> -- /media/bitlocker
> sudo mount -o loop /media/bitlocker/dislocker-file /media/bitlockermount
> ```
> 
> 💡 Flow:
> 
> - Attach disk → decrypt → mount decrypted filesystem
>     

---

> [!info] 🧹 Unmount
> 
> ```bash
> sudo umount /media/bitlockermount
> sudo umount /media/bitlocker
> ```

---

# 🧰 Tools Summary

---

|Tool|Purpose|
|:--|:--|
|`file`|Identify archive type|
|`zip2john`|Extract ZIP hashes|
|`bitlocker2john`|Extract BitLocker hashes|
|`john`|Password cracking (CPU-based)|
|`hashcat`|GPU password cracking|
|`openssl`|Decrypt OpenSSL-encrypted files|
|`dislocker`|Mount BitLocker volumes|

---

# ⚠️ Key Security Insights

---

> [!danger] 🔐 Why attacks succeed
> 
> - Weak human passwords
>     
> - Reused credentials
>     
> - Low-entropy wordlists (e.g. rockyou.txt)
>     

---

> [!info] 🧠 Core Truth
> 
> ```bash
> Encryption (AES, etc.) is not being broken
> Passwords protecting it are
> ```
> 
> ✔ Strong encryption ≠ strong security if password is weak

---

