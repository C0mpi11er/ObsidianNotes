
Perfect! Here's a **much more detailed and expanded cheat sheet**, including **additional protocols**, **full port mapping**, and a **longer list of functions/commands** per protocol — ideal for hacking, networking exams, or deep protocol analysis.

---

## 🧠 **💣 ULTIMATE NETWORK PROTOCOLS CHEAT SHEET**

|Protocol|Port|Transport|Description|Common Commands / Functions|
|---|---|---|---|---|
|**FTP**|21 (cmd), 20 (data)|TCP|File Transfer Protocol|`USER`, `PASS`, `LIST`, `RETR`, `STOR`, `DELE`, `CWD`, `MKD`, `RMD`, `PWD`, `QUIT`, `TYPE`, `PASV`, `PORT`, `NOOP`|
|**SFTP (via SSH)**|22|TCP|Secure file transfer|`ls`, `cd`, `put`, `get`, `mkdir`, `rm`, `rmdir`, `exit`, `rename`, `stat`, `chmod`, `chown`|
|**TFTP**|69|UDP|Lightweight FTP|`RRQ`, `WRQ`, `DATA`, `ACK`, `ERROR` (protocol messages)|
|**HTTP**|80|TCP|Web content delivery|`GET`, `POST`, `HEAD`, `PUT`, `DELETE`, `CONNECT`, `OPTIONS`, `TRACE`, `PATCH`|
|**HTTPS**|443|TCP|Secure HTTP|Same as HTTP + TLS handshake|
|**SMTP**|25 / 465 / 587|TCP|Mail delivery (servers)|`HELO`, `EHLO`, `MAIL FROM:`, `RCPT TO:`, `DATA`, `RSET`, `VRFY`, `EXPN`, `NOOP`, `QUIT`, `AUTH`|
|**POP3**|110|TCP|Retrieve email (download)|`USER`, `PASS`, `STAT`, `LIST`, `RETR`, `DELE`, `NOOP`, `QUIT`, `TOP`, `UIDL`|
|**IMAP**|143 / 993|TCP|Read email (server-side)|`LOGIN`, `LIST`, `SELECT`, `FETCH`, `STORE`, `SEARCH`, `LOGOUT`, `UID`, `COPY`, `EXPUNGE`, `APPEND`, `IDLE`|
|**DNS**|53|UDP/TCP|Domain resolution|`QUERY`, `RESPONSE` with types like `A`, `AAAA`, `MX`, `TXT`, `PTR`, `NS`, `SOA`, `CNAME`, `AXFR`, `IXFR`|
|**DHCP**|67/68|UDP|Dynamic IP config|`DISCOVER`, `OFFER`, `REQUEST`, `ACK`, `NAK`, `DECLINE`, `INFORM`, `RELEASE`|
|**Telnet**|23|TCP|Remote terminal|Shell commands; protocol uses `IAC`, `DO`, `DONT`, `WILL`, `WONT`|
|**SSH**|22|TCP|Secure remote shell|`ssh user@host`, shell commands, SCP/SFTP subsystem, key exchange|
|**NTP**|123|UDP|Clock sync|Request/Response format with `Leap Indicator`, `Stratum`, `Transmit Timestamp`|
|**LDAP**|389 / 636|TCP|Directory access|`BIND`, `SEARCH`, `COMPARE`, `ADD`, `DELETE`, `MODIFY`, `ABANDON`, `EXTENDED`, `UNBIND`|
|**Kerberos**|88|TCP/UDP|Authentication|`AS-REQ`, `AS-REP`, `TGS-REQ`, `TGS-REP`, `AP-REQ`, `AP-REP`, `KRB-ERROR`|
|**SMB**|445|TCP|Windows file sharing|`NEGOTIATE`, `SESSION_SETUP`, `TREE_CONNECT`, `NT_CREATE`, `READ`, `WRITE`, `TRANS`, `ECHO`, `CLOSE`|
|**NetBIOS**|137-139|UDP/TCP|Legacy file sharing|`NAME QUERY`, `NAME REGISTRATION`, `SESSION REQUEST`, `DATA`|
|**RDP**|3389|TCP|Remote desktop|Graphical session, protocol includes `X.224`, `T.125`, and input events|
|**MySQL**|3306|TCP|Relational DB|`SELECT`, `INSERT`, `UPDATE`, `DELETE`, `SHOW`, `CREATE`, `DROP`, `GRANT`, `REVOKE`, `DESCRIBE`, `ALTER`, `USE`, `FLUSH`|
|**PostgreSQL**|5432|TCP|Relational DB|Same as MySQL + `BEGIN`, `COMMIT`, `ROLLBACK`, `ANALYZE`, `EXPLAIN`, `VACUUM`|
|**Oracle DB**|1521|TCP|Oracle DB server|`SELECT`, `FROM`, `EXEC`, `DESCRIBE`, `PL/SQL BLOCKS`, `ALTER`, `GRANT`, `MERGE`, `TRUNCATE`|
|**MSSQL**|1433|TCP|MS SQL Server|`USE`, `SELECT`, `EXEC`, `xp_cmdshell`, `sp_configure`, `sp_who`, `sp_help`|
|**Redis**|6379|TCP|Key-value store|`GET`, `SET`, `DEL`, `INCR`, `DECR`, `KEYS`, `FLUSHALL`, `AUTH`, `PING`, `EXPIRE`, `TTL`, `LPUSH`, `LRANGE`|
|**MongoDB**|27017|TCP|NoSQL DB|`find()`, `insert()`, `update()`, `deleteOne()`, `aggregate()`, `db.stats()`, `db.collection.stats()`|
|**ElasticSearch**|9200 / 9300|TCP|REST-based search engine|`GET /_search`, `POST /index/_doc`, `PUT`, `DELETE`, `HEAD`, `PATCH`, `BULK`, `AGGREGATIONS`|
|**VNC**|5900+N|TCP|Remote desktop sharing|Keyboard/mouse events, `SetPixelFormat`, `FramebufferUpdateRequest`|
|**IRC**|6667|TCP|Chat protocol|`NICK`, `USER`, `JOIN`, `PRIVMSG`, `NOTICE`, `PART`, `PING`, `PONG`, `QUIT`, `TOPIC`, `MODE`|
|**BitTorrent**|6881–6889|TCP/UDP|P2P sharing|`handshake`, `interested`, `have`, `request`, `piece`, `cancel`, `choke`|
|**OpenVPN**|1194|UDP|VPN tunneling|TLS-based: `ClientHello`, `ServerHello`, `ChangeCipherSpec`, `Data`|
|**WireGuard**|51820|UDP|Modern VPN|Key exchange, minimal messages: `Handshake Initiation`, `Response`, `Transport`|
|**Zabbix Agent**|10050|TCP|Monitoring|`system.cpu.load`, `net.if.in`, `agent.ping`, custom keys|
|**Grafana**|3000|TCP|Dashboards|HTTP REST API: `GET /api`, `POST /api/dashboards`, `PUT`, `DELETE`|
|**Prometheus**|9090|TCP|Time series monitoring|PromQL: `rate()`, `avg_over_time()`, `sum()`, `label_replace()`, `up`|
|**Docker API**|2375 (insecure), 2376 (TLS)|TCP|Container control|`GET /containers/json`, `POST /containers/create`, `DELETE /containers/{id}`|
|**RPC (Sun/ONC)**|111|TCP/UDP|Remote procedure calls|`GETPORT`, `NULL`, `SET`, used in NFS|
|**NFS**|2049|TCP/UDP|Network file system|`LOOKUP`, `READ`, `WRITE`, `CREATE`, `REMOVE`, `RENAME`, `STATFS`, `FSINFO`|
|**SMTPS**|465|TCP|Secure email (SSL)|SMTP commands wrapped in SSL|
|**SNMP**|161|UDP|Network monitoring|`GET`, `GETNEXT`, `GETBULK`, `SET`, `TRAP`, `WALK`, `INFORM`|
|**SNMP Trap**|162|UDP|Alert from SNMP agents|`TRAP` messages|
|**mDNS**|5353|UDP|Local name resolution|`QUERY`, `RESPONSE` with DNS-like records|
|**Syslog**|514|UDP/TCP|Log transport|Facility.Level, message structure: `<PRI>Timestamp Hostname Appname Message`|

---

### 💡 Want it as a **file or formatted doc**?

I can generate this into:

- 📄 PDF (print-friendly)
    
- 📜 Markdown (for docs or GitHub)
    
- 📊 Excel/CSV (for spreadsheets)
    
- 🌐 HTML (for a personal cheatsheet site)
    

Just tell me the format and I’ll build it for you!