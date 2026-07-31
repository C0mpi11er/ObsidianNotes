# 🛰️ Cracking Protected Archives

## Tips for Success

[!INFO]
1. **Use targeted wordlists** - Include company names, dates, common passwords  
2. **Try common patterns** - company123, Company2024!, etc.  
3. **Check file metadata** - May contain hints about creator/purpose  
4. **Multiple attack methods** - Dictionary, rules, mask attacks  
5. **Be patient** - Some files take significant time to crack  
6. **Check for weak passwords** - Many users still use simple passwords  
7. **Corporate patterns** - Often follow predictable formats  

---

## Cracking Protected Archives

### ZIP Files (Extended)
```bash
# Extract hash from ZIP
zip2john ZIP.zip > zip.hash

# Check hash format
cat zip.hash

# Crack with John
john --wordlist=rockyou.txt zip.hash

# Show results
john zip.hash --show
```

### OpenSSL Encrypted GZIP Files
```bash
# Check if file is OpenSSL encrypted
file GZIP.gzip

# Direct brute force with OpenSSL
for i in $(cat rockyou.txt); do 
    openssl enc -aes-256-cbc -d -in GZIP.gzip -k $i 2>/dev/null | tar xz
done
```

### BitLocker Encrypted Drives
```bash
# Extract hashes from BitLocker VHD
bitlocker2john -i Backup.vhd > backup.hashes

# Get password hash (first hash)
grep "bitlocker\$0" backup.hashes > backup.hash

# Crack with hashcat (mode 22100)
hashcat -a 0 -m 22100 backup.hash /usr/share/wordlists/rockyou.txt

# Show results
hashcat -m 22100 backup.hash --show
```

### Mounting BitLocker Drives

#### Windows
[!SUCCESS]
1. Double-click the .vhd file  
2. Double-click the BitLocker volume  
3. Enter the cracked password  

#### Linux/macOS
```bash
# Install dislocker
sudo apt-get install dislocker

# Create mount directories
sudo mkdir -p /media/bitlocker
sudo mkdir -p /media/bitlockermount

# Set up loop device
sudo losetup -f -P Backup.vhd

# Decrypt with dislocker
sudo dislocker /dev/loop0p2 -u<password> -- /media/bitlocker

# Mount the decrypted volume
sudo mount -o loop /media/bitlocker/dislocker-file /media/bitlockermount

# Browse files
cd /media/bitlockermount/
ls -la

# Unmount when done
sudo umount /media/bitlockermount
sudo umount /media/bitlocker
```

### Practical BitLocker Example

[!EXAMPLE]
**Complete workflow for cracking and mounting a BitLocker VHD:**

```bash
# Step 1: Download and extract the VHD
wget http://target:port/download -O download.zip
unzip download.zip

# Step 2: Extract BitLocker hash and crack password
bitlocker2john -i Private.vhd > private.hashes
grep "bitlocker\$0" private.hashes > private.hash
hashcat -a 0 -m 22100 private.hash /usr/share/wordlists/rockyou.txt

# Step 3: Create mount directories
sudo mkdir -p /media/bitlocker
sudo mkdir -p /media/bitlockermount

# Step 4: Set up loop device
sudo losetup -f -P Private.vhd

# Step 5: Verify loop device
losetup --all
# Output: /dev/loop0: []: (/home/user/Private.vhd)

# Step 6: Install dislocker (if not already installed)
sudo apt-get install dislocker -y

# Step 7: Decrypt with cracked password
sudo dislocker /dev/loop0p1 -u<cracked_password> -- /media/bitlocker

# Step 8: Verify decryption
sudo ls -la /media/bitlocker
# Should show dislocker-file

# Step 9: Mount the decrypted volume
sudo mount -o loop /media/bitlocker/dislocker-file /media/bitlockermount

# Step 10: Access files
cd /media/bitlockermount
cat flag.txt

# Step 11: Cleanup when done
sudo umount /media/bitlockermount
sudo umount /media/bitlocker
sudo losetup -d /dev/loop0
```

**Key Points:**  
- Use `losetup --all` to verify loop device assignment  
- BitLocker partitions are usually `p1` or `p2` (e.g., `/dev/loop0p1`)  
- The `dislocker-file` is created in the first mount point  
- Always unmount and detach loop devices when finished  

---

### Common Archive Types
[!INFO]
- **.zip** - ZIP archives  
- **.rar** - RAR archives  
- **.7z** - 7-Zip archives  
- **.tar.gz** - Tarball with gzip  
- **.tar.bz2** - Tarball with bzip2  
- **.vhd/.vhdx** - Virtual Hard Disk (often BitLocker)  
- **.vmdk** - VMware Virtual Disk  
- **.truecrypt** - TrueCrypt volumes  
- **.luks** - Linux Unified Key Setup  

### Additional Archive Hash Modes
| Archive Type | Tool | Hashcat Mode |
|-------------|------|--------------|
| BitLocker   | bitlocker2john | 22100    |
| TrueCrypt   | truecrypt_volume2john | 6211      |
| LUKS        | luks2john     | 14600    |
| VMware VMDK | vmware2john   | 20300    |

---

## Automation Script Example
```bash
#!/bin/bash
# Auto-crack common protected files and archives

for file in $(find . -name "*.pdf" -o -name "*.docx" -o -name "*.zip" -o -name "*.vhd"); do
    echo "Processing: $file"
    
    case "$file" in
        *.pdf)
            pdf2john.py "$file" > "${file}.hash"
            john --wordlist=rockyou.txt "${file}.hash"
            ;;
        *.docx)
            office2john.py "$file" > "${file}.hash"
            john --wordlist=rockyou.txt "${file}.hash"
            ;;
        *.zip)
            zip2john "$file" > "${file}.hash"
            john --wordlist=rockyou.txt "${file}.hash"
            ;;
        *.vhd)
            bitlocker2john -i "$file" > "${file}.hashes"
            grep "bitlocker\$0" "${file}.hashes" > "${file}.hash"
            hashcat -a 0 -m 22100 "${file}.hash" /usr/share/wordlists/rockyou.txt
            ;;
    esac
done
```