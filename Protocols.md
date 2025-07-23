
Perfect! Here's a **much more detailed and expanded cheat sheet**, including **additional protocols**, **full port mapping**, and a **longer list of functions/commands** per protocol — ideal for hacking, networking exams, or deep protocol analysis.

---
Absolutely! Here's an **extensive and detailed** table of **common network protocols**, their **secure versions**, **default ports**, **transport layers**, and **important commands/functions** used. This is useful for cybersecurity, pentesting, networking exams, and general knowledge.

---

## 🌐 **Ultimate Protocol Cheat Sheet (Standard + Secure)**

| Protocol               | Secure Version              | Port                                     | Transport | Encryption                | Usage / Purpose                         | Key Commands / Functions                                                                   |
| ---------------------- | --------------------------- | ---------------------------------------- | --------- | ------------------------- | --------------------------------------- | ------------------------------------------------------------------------------------------ |
| **HTTP**               | **HTTPS**                   | 80 / 443                                 | TCP       | HTTPS: TLS/SSL            | Web browsing, REST APIs                 | `GET`, `POST`, `PUT`, `DELETE`, `HEAD`, `OPTIONS`, `CONNECT`, `TRACE`, `PATCH`             |
| **FTP**                | **FTPS / SFTP**             | 21 (FTP), 990 (FTPS implicit), 22 (SFTP) | TCP       | FTPS: TLS/SSL, SFTP: SSH  | File transfers                          | `USER`, `PASS`, `LIST`, `RETR`, `STOR`, `PWD`, `CWD`, `QUIT`, `PASV`, `PORT`               |
| **SMTP**               | **SMTPS / STARTTLS**        | 25, 465 (SMTPS), 587 (STARTTLS)          | TCP       | SSL/TLS                   | Sending email                           | `HELO`, `EHLO`, `MAIL FROM`, `RCPT TO`, `DATA`, `RSET`, `VRFY`, `EXPN`, `QUIT`, `STARTTLS` |
| **POP3**               | **POP3S**                   | 110 / 995                                | TCP       | SSL/TLS                   | Email retrieval (download)              | `USER`, `PASS`, `STAT`, `LIST`, `RETR`, `DELE`, `QUIT`, `TOP`, `UIDL`                      |
| **IMAP**               | **IMAPS**                   | 143 / 993                                | TCP       | SSL/TLS                   | Email synchronization                   | `LOGIN`, `SELECT`, `FETCH`, `STORE`, `SEARCH`, `LOGOUT`, `EXPUNGE`, `CAPABILITY`           |
| **Telnet**             | **SSH**                     | 23 / 22                                  | TCP       | SSH                       | Remote shell access                     | Text-based interaction (Telnet: plaintext, SSH: secure shell)                              |
| **LDAP**               | **LDAPS / LDAP + STARTTLS** | 389 (LDAP), 636 (LDAPS)                  | TCP       | SSL/TLS                   | Directory access (AD, user directories) | `BIND`, `SEARCH`, `ADD`, `DELETE`, `MODIFY`, `UNBIND`                                      |
| **DNS**                | **DoT / DoH**               | 53 (DNS), 853 (DoT), 443 (DoH)           | UDP / TCP | TLS / HTTPS               | Domain resolution                       | `A`, `AAAA`, `MX`, `NS`, `PTR`, `CNAME`, `SOA`, `TXT`, `ANY`, `DIG`, `NSLOOKUP`            |
| **SNMPv1/v2**          | **SNMPv3**                  | 161 / 162 (trap)                         | UDP       | SNMPv3: encryption & auth | Device monitoring                       | `GET`, `GETNEXT`, `SET`, `TRAP`, `WALK`, `INFO`, `BULKGET`, `BULKWALK`                     |
| **RDP**                | (Encrypted by default)      | 3389                                     | TCP       | TLS/SSL                   | Remote desktop (Windows)                | GUI-based interaction; NLA (Network Level Auth)                                            |
| **Syslog**             | **Syslog over TLS**         | 514 (UDP), 6514 (TCP)                    | UDP / TCP | TLS                       | Centralized logging                     | Structured log messages in RFC 5424 format                                                 |
| **TFTP**               | -                           | 69                                       | UDP       | None                      | Simple file transfer                    | `RRQ`, `WRQ`, `DATA`, `ACK`, `ERROR`                                                       |
| **NetBIOS**            | -                           | 137, 138, 139                            | UDP / TCP | None                      | Windows file/printer sharing            | `NBSTAT`, file/resource announcements                                                      |
| **SMB**                | **SMB over TLS (SMB 3.x)**  | 445                                      | TCP       | SMBv3 supports encryption | Windows file sharing                    | File operations, `TREE CONNECT`, `SESSION SETUP`, `NEGOTIATE`                              |
| **NFS**                | **NFSv4 (w/ Kerberos)**     | 2049                                     | TCP       | Kerberos optional         | UNIX/Linux file sharing                 | `mount`, `umount`, `rpcbind`, `showmount`                                                  |
| **Kerberos**           | -                           | 88                                       | TCP / UDP | Built-in encryption       | Authentication in AD & Linux            | `AS-REQ`, `AS-REP`, `TGS-REQ`, `TGS-REP`, ticket exchanges                                 |
| **HTTPS**              | (Already secure)            | 443                                      | TCP       | TLS/SSL                   | Secure web access                       | Same as HTTP, just encrypted                                                               |
| **SFTP**               | (SSH-based)                 | 22                                       | TCP       | SSH                       | Secure file transfer                    | `put`, `get`, `ls`, `rm`, `rename`, `cd`, `lcd`, `exit`, `help`                            |
| **FTPS**               | (SSL-based)                 | 21 / 990                                 | TCP       | SSL/TLS                   | Secure FTP                              | Same FTP commands wrapped in SSL                                                           |
| **MSSQL**              | Encrypted Option            | 1433                                     | TCP       | Optional TLS              | SQL Server DB access                    | `SELECT`, `INSERT`, `UPDATE`, `DELETE`, `EXEC`, `CREATE`, `DROP`                           |
| **MySQL**              | Encrypted Option            | 3306                                     | TCP       | Optional SSL              | MySQL DB access                         | SQL commands: `SELECT`, `SHOW`, `USE`, `GRANT`, `REVOKE`                                   |
| **PostgreSQL**         | Encrypted Option            | 5432                                     | TCP       | SSL optional              | PostgreSQL DB access                    | SQL commands: `SELECT`, `ALTER`, `CREATE`, `GRANT`, `BEGIN`, `COMMIT`                      |
| **Redis**              | TLS-enabled manually        | 6379 / custom                            | TCP       | TLS optional              | In-memory key/value store               | `SET`, `GET`, `DEL`, `EXPIRE`, `AUTH`, `FLUSHALL`                                          |
| **MongoDB**            | TLS-enabled manually        | 27017                                    | TCP       | TLS optional              | NoSQL DB                                | `find()`, `insertOne()`, `updateOne()`, `aggregate()`                                      |
| **NTP**                | -                           | 123                                      | UDP       | None / optional auth      | Time sync                               | `ntpq`, `ntpdate`, `ntpd`, `timedatectl`                                                   |
| **RADIUS**             | **RadSec**                  | 1812 / 1813 (acct)                       | UDP       | RadSec uses TLS           | Network authentication (802.1X)         | `Access-Request`, `Access-Accept`, `Access-Reject`, `Accounting-Request`                   |
| **TACACS+**            | -                           | 49                                       | TCP       | Encrypted body            | Authentication & authorization          | `Login`, `Response`, `Authorize`, `Accounting`                                             |
| **OpenVPN**            | -                           | 1194                                     | UDP / TCP | TLS/SSL                   | VPN tunnels                             | Client config, TLS handshake, cert verification                                            |
| **IPSec (IKEv2, ESP)** | -                           | UDP 500 / 4500                           | UDP       | IP encryption layer       | Secure network tunneling                | Uses `SA`, `ESP`, `AH`, `IKE`, `ISAKMP`                                                    |
| **WireGuard**          | -                           | UDP 51820                                | UDP       | ChaCha20 Poly1305         | Fast VPN protocol                       | Uses keys, peer configs, no commands                                                       |

---

## 🧪 Command Use Cases

- **HTTP (GET/POST):** `curl -X GET https://example.com`
    
- **FTP:** `ftp> USER admin`, `ftp> PASS password`
    
- **SMTP:** `telnet smtp.example.com 25`, then `HELO`, `MAIL FROM`, `RCPT TO`
    
- **POP3:** `USER admin`, `PASS secret`, `RETR 1`
    
- **IMAP:** `a001 LOGIN user pass`, `a002 SELECT INBOX`
    
- **SSH:** `ssh user@192.168.1.1`
    
- **SFTP:** `sftp user@host`, then `put file.txt`
    
- **LDAP:** `ldapsearch -x`, `ldapadd`, `ldapmodify`
    
- **DNS:** `dig example.com ANY`, `nslookup`, `host`
    

---

Would you like this formatted as:

- 📄 PDF
    
- 📑 Markdown (for GitHub/Docs)
    
- 📊 CSV/Excel (for spreadsheets)
    
- 🧾 Poster-style cheat sheet?
    

Let me know your preferred format and I'll export it for you!