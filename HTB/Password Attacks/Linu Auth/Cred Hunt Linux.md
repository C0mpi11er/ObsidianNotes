
> [!ABSTRACT] Overview  
> Credential hunting = systematically searching a Linux system for **reusable authentication material** after initial access (reverse shell, web shell, etc.).

**Goal**

- Escalate privileges
    
- Reuse credentials across services
    
- Pivot internally
    

---

## Credential Sources

> [!INFO] Main Categories

Credentials are typically found in:

- Files (configs, scripts, SSH keys, DBs)
    
- History (bash history, logs)
    
- Memory (process cache, runtime secrets)
    
- Keyrings (browser / OS password stores)
    

---

> [!TIP] Pentest mindset  
> Think in terms of:  
> “Where would a human accidentally store a password?”

---

## FILE-BASED HUNTING

> [!ABSTRACT] Core principle  
> Linux treats **everything as a file**, so credentials often live in plain text somewhere on disk.

---

## Configuration files

> [!INFO] Common targets  
> Look for:

- `.conf`
    
- `.config`
    
- `.cnf`
    

---

> [!EXAMPLE] Search config files

```bash
for l in .conf .config .cnf; do
  find / -name "*$l" 2>/dev/null
done
```

---

> [!CHECK] Grep for credentials

```bash
grep -R "user\|password\|pass" /etc 2>/dev/null
```

---

## Database files

> [!INFO] Common extensions

- `.db`
    
- `.sql`
    
- `*.db*`
    

---

> [!EXAMPLE] Find DB files

```bash
find / -name "*.db*" 2>/dev/null
```

---

## Notes & user files

> [!INFO] Where humans leak creds

Look in:

- `.txt`
    
- hidden files (`.*`)
    
- home directories
    

---

> [!EXAMPLE] Search notes

```bash
find /home -type f -name "*.txt" -o ! -name "*.*"
```

---

## Scripts (HIGH VALUE)

> [!WARNING] Critical exposure point  
> Scripts often contain **hardcoded credentials**.

---

> [!EXAMPLE] Search scripts

```bash
for l in .sh .py .pl .go .c .jar; do
  find / -name "*$l" 2>/dev/null
done
```

---

## Cron jobs

> [!INFO] Automated execution risk  
> Cronjobs often include:

- backup scripts
    
- admin tasks
    
- embedded passwords
    

---

> [!EXAMPLE] Check cron system-wide

```bash
cat /etc/crontab
ls -la /etc/cron.*
```

---

## History files

> [!ATTENTION] High-value target  
> Users frequently leak passwords in shell history.

---

> [!EXAMPLE] Bash history

```bash
cat ~/.bash_history
```

---

## LOG FILES

> [!INFO] System activity monitoring  
> Logs often expose:

- login attempts
    
- sudo usage
    
- SSH authentication
    

---

> [!EXAMPLE] Search logs

```bash
grep -R "failed\|accepted\|sudo\|password" /var/log 2>/dev/null
```

---

## MEMORY & STORED CREDENTIALS

> [!WARNING] Requires elevated privileges  
> Some tools extract live credentials from memory.

---

### mimipenguin

> [!EXAMPLE] Extract session creds

```bash
sudo python3 mimipenguin.py
```

---

## LAZAGNE (CREDENTIAL MINER)

> [!ABSTRACT] Multi-source credential extraction tool  
> Targets:

- browsers
    
- SSH
    
- WiFi
    
- keyrings
    
- databases
    
- system storage
    

---

> [!EXAMPLE] Run LaZagne

```bash
python3 laZagne.py all
```

---

> [!CHECK] Browser mode only

```bash
python3 laZagne.py browsers
```

---

## Browser credentials

> [!INFO] Common leak vector  
> Browsers store:

- saved passwords
    
- encrypted logins (`logins.json`)
    

Example path:

```bash
~/.mozilla/firefox/
```

---

> [!EXAMPLE] View stored credentials file

```bash
cat logins.json | jq .
```

---

> [!TIP] Decryption tools

- Firefox Decrypt
    
- LaZagne (automatic extraction)
    

---

## TLDR

> [!TLDR] Credential hunting workflow

1. Check configs (`/etc`, `/opt`, `/var/www`)
    
2. Search scripts for hardcoded secrets
    
3. Check cron jobs
    
4. Inspect history files
    
5. Scan logs for authentication data
    
6. Run automation tools (LaZagne, mimipenguin)
    
7. Check browser/keyring storage
    

---

> [!QUESTION] Exam trigger mindset  
> If you land on a Linux box:

> “Where did the admin forget to sanitize credentials?”

That’s the entire strategy behind credential hunting.