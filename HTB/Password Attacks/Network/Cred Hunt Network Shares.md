## Credential Hunting in Network Shares — Cheat Sheet

> [!ABSTRACT] What it is  
> Hunt **plaintext creds, configs, secrets, and sensitive files** inside SMB shares.

Primary tools:

- **Snaffler** (Windows, domain joined)
    
- **PowerHuntShares** (Windows)
    
- **MANSPIDER** (Linux)
    
- **NetExec spider** (Linux)
    

---

# > [!INFO] High-Value Search Targets

## Keywords

```text
passw
user
token
secret
key
cred
login
```

Domain-specific:

```text
INLANEFREIGHT\
```

---

## Valuable extensions

```text
.ini
.cfg
.config
.env
.xml
.txt
.ps1
.bat
.xlsx
.docx
```

---

## Interesting filenames

```text
config
password
creds
initial
backup
deploy
unattend
```

---

# > [!SUCCESS] Windows Hunting

## Snaffler (basic)

```powershell
Snaffler.exe -s
```

Scans:

- domain computers
    
- accessible SMB shares
    
- sensitive files
    

---

## Include AD usernames

```powershell
Snaffler.exe -s -u
```

Good for finding:

- embedded usernames
    
- references inside configs
    

---

## Target specific shares

```powershell
Snaffler.exe -s -i IT,Finance
```

---

## Exclude noisy shares

```powershell
Snaffler.exe -s -n Marketing,Photos
```

---

# > [!INFO] PowerHuntShares

## Basic scan

```powershell
Invoke-HuntSMBShares -Threads 100 -OutputDirectory C:\Users\Public
```

Generates:

- HTML report
    
- CSV findings
    
- permission analysis
    

Best for:

- large environments
    
- visual review
    

---

# > [!SUCCESS] Linux Hunting

## MANSPIDER basic content search

```bash
docker run --rm -v ./manspider:/root/.manspider \
blacklanternsecurity/manspider \
10.129.234.121 \
-c 'passw' \
-u 'mendres' \
-p 'Inlanefreight2025!'
```

---

## Better regex hunt

```bash
manspider 10.129.234.121 \
-u mendres \
-p 'Inlanefreight2025!' \
--regex "passw|token|secret|cred"
```

---

## Hunt specific extensions

```bash
manspider 10.129.234.121 \
-u mendres \
-p 'Inlanefreight2025!' \
--extensions txt,xml,ini,config,ps1
```

---

## Focus on IT share

```bash
manspider 10.129.234.121 \
-u mendres \
-p 'Inlanefreight2025!' \
-s IT
```

---

# > [!INFO] NetExec Spidering

## Search file contents

```bash
nxc smb 10.129.234.121 \
-u mendres \
-p 'Inlanefreight2025!' \
--spider IT \
--content \
--pattern "passw"
```

---

## Hunt usernames

```bash
nxc smb 10.129.234.121 \
-u mendres \
-p 'Inlanefreight2025!' \
--spider IT \
--content \
--pattern "INLANEFREIGHT\\"
```

---

## Enumerate shares first

```bash
nxc smb 10.129.234.121 \
-u mendres \
-p 'Inlanefreight2025!' \
--shares
```

---

# > [!CHECK] Best Search Order

## 1. Enumerate

```bash
nxc smb target -u user -p pass --shares
```

---

## 2. Hit IT / Finance first

High-value shares:

- IT
    
- Finance
    
- SYSVOL
    
- NETLOGON
    

---

## 3. Fast content hunt

```bash
nxc smb target --spider IT --content --pattern "passw"
```

---

## 4. Deep crawl

```bash
manspider target -u user -p pass --regex "passw|secret|token"
```

---

## 5. Windows local scan

```powershell
Snaffler.exe -s
```

---

# > [!WARNING] Highest Yield Files

Look for:

```text
unattend.xml
Groups.xml
web.config
appsettings.json
deploy.ps1
backup.ini
```

These often contain:

- plaintext creds
    
- service account passwords
    
- deployment secrets
    

---

# > [!TLDR] HTB Default Workflow

## Linux

```bash
nxc smb target -u user -p pass --shares
```

```bash
nxc smb target --spider IT --content --pattern "passw"
```

```bash
manspider target -u user -p pass --regex "passw|secret|cred"
```

---

## Windows

```powershell
Snaffler.exe -s
```

```powershell
Invoke-HuntSMBShares -Threads 100
```

**Default target:**  
Hit **IT share first**. That’s where creds usually leak.