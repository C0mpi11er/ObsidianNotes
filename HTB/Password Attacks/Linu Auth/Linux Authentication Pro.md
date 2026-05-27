## Linux Authentication Process

> [!ABSTRACT] Overview  
> Linux authentication is primarily handled by **PAM (Pluggable Authentication Modules)**.

**Purpose**

- User authentication
    
- Session management
    
- Password changes
    
- Account restrictions
    

**Common PAM modules**

```bash
/usr/lib/x86_64-linux-gnu/security/
```

Examples:

```bash
pam_unix.so
pam_unix2.so
```

Used by commands like:

```bash
passwd
su
login
sudo
```

---

> [!INFO] PAM Flow

When a user changes password:

```bash
passwd
```

Flow:

1. `passwd` calls PAM
    
2. PAM validates policy
    
3. Updates password hash
    
4. Stores hash in:
    

```bash
/etc/shadow
```

PAM also supports:

- LDAP
    
- Kerberos
    
- Mount auth modules
    

---

## `/etc/passwd`

> [!INFO] User Account Database  
> World-readable file containing user account metadata.

Location:

```bash
/etc/passwd
```

Example:

```bash
htb-student:x:1000:1000:,,,:/home/htb-student:/bin/bash
```

### Fields

|Field|Meaning|
|---|---|
|Username|Account name|
|Password|Usually `x`|
|UID|User ID|
|GID|Group ID|
|GECOS|User description|
|Home|Home directory|
|Shell|Default shell|

---

> [!ATTENTION] Password Field

Common values:

```bash
x
```

→ Password stored in `/etc/shadow`

Empty:

```bash
root::
```

→ No password required

Hash directly present:

```bash
$6$...
```

→ Old/insecure system config

---

> [!DANGER] Writable `/etc/passwd`

If writable, attacker can remove root password:

Before:

```bash
root:x:0:0:root:/root:/bin/bash
```

After:

```bash
root::0:0:root:/root:/bin/bash
```

Then:

```bash
su
```

Gain:

```bash
root shell
```

---

> [!CHECK] Verify Permissions

```bash
ls -l /etc/passwd
```

Secure:

```bash
-rw-r--r--
```

---

## `/etc/shadow`

> [!INFO] Secure Password Storage

Stores password hashes and aging info.

Location:

```bash
/etc/shadow
```

Readable only by root.

---

Example:

```bash
htb-student:$y$j9T$HASH:18955:0:99999:7:::
```

### Fields

|Field|Meaning|
|---|---|
|Username|Account name|
|Password Hash|Encrypted password|
|Last Change|Days since epoch|
|Min Age|Minimum password age|
|Max Age|Expiration period|
|Warning|Expiry warning days|
|Inactive|Disabled after expiry|
|Expiration|Account expiry|
|Reserved|Unused|

---

> [!CAUTION] Special Password Values

```bash
!
*
```

Meaning:

- Unix password login disabled
    

Still possible:

- SSH keys
    
- Kerberos
    
- Other auth methods
    

Empty field:

```bash
::
```

Meaning:

- No password required
    

---

## Hash Format

> [!ABSTRACT] Linux Hash Structure

Format:

```bash
$id$salt$hash
```

---

### Common IDs

|ID|Algorithm|
|---|---|
|1|MD5|
|2a|Blowfish|
|5|SHA-256|
|6|SHA-512|
|sha1|SHA1crypt|
|y|Yescrypt|
|gy|Gost-yescrypt|
|7|Scrypt|

---

> [!ATTENTION] Important for Cracking

Modern systems:

```bash
y
```

(Yescrypt)

Older systems:

```bash
1
6
```

Older hashes are often easier to crack.

---

## `/etc/security/opasswd`

> [!INFO] Old Password History

Stores previously used passwords.

Location:

```bash
/etc/security/opasswd
```

Used to prevent password reuse.

---

Example:

```bash
cry0l1t3:1000:2:$1$HASH1,$1$HASH2
```

---

> [!CHECK] Why It Matters

If old hashes are weak (MD5):

```bash
$1$
```

You may:

- Crack previous passwords
    
- Identify password patterns
    
- Predict current credentials
    

---

## Cracking Linux Credentials

> [!SUCCESS] Extract Hashes

Backup files:

```bash
sudo cp /etc/passwd /tmp/passwd.bak
sudo cp /etc/shadow /tmp/shadow.bak
```

Combine:

```bash
unshadow /tmp/passwd.bak /tmp/shadow.bak > /tmp/unshadowed.hashes
```

---

> [!EXAMPLE] Crack with Hashcat

```bash
hashcat -m 1800 -a 0 /tmp/unshadowed.hashes rockyou.txt -o /tmp/cracked
```

Flags:

- `-m 1800` → SHA-512
    
- `-a 0` → Dictionary attack
    

---

> [!NOTE] JtR Alternative

```bash
john --single /tmp/unshadowed.hashes
```

Best when:

- Full passwd context available
    
- Username-based mutations help
    

---

> [!TLDR] Pentest Enumeration Checklist

Check:

```bash
ls -l /etc/passwd
ls -l /etc/shadow
cat /etc/passwd
sudo cat /etc/shadow
sudo cat /etc/security/opasswd
```

Look for:

- Writable `/etc/passwd`
    
- Empty password fields
    
- Weak hash algorithms
    
- Reused password patterns
    
- Old crackable hashes
    

---

> [!QUESTION] Exam Trigger  
> If you gain root or sudo, immediately check:

1. `/etc/shadow`
    
2. `/etc/security/opasswd`
    
3. Hash algorithm type
    
4. Reusable creds across services
    

This is a classic **Linux privilege escalation / credential harvesting path**.