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

| Option     | Description                                                    |
| ---------- | -------------------------------------------------------------- |
| `-l`       | Single username                                                |
| `-L`       | Username list file                                             |
| `-p`       | Single password                                                |
| `-P`       | Password list file                                             |
| `-s PORT`  | Custom port                                                    |
| `-t TASKS` | Number of parallel tasks (default: 16)                         |
| `-V`       | Verbose output (shows each login attempt)                      |
| `-vV`      | Super verbose (all output + attempt detail)                    |
| `-f`       | Exit when a valid login is found                               |
| `-o FILE`  | Output file                                                    |
| `-I`       | Ignore "invalid" responses for some protocols                  |
| `-w TIME`  | Wait timeout for response in seconds                           |
| `-e nsr`   | Try `n` (null), `s` (same as username), `r` (reverse username) |
| `-R`       | Restore from a saved session (`hydra.restore`)                 |

---

### 🔁 **Brute-Force Modes**

| Mode                            | Usage                          |
| ------------------------------- | ------------------------------ |
| Single user, wordlist           | `-l user -P passlist.txt`      |
| Wordlist of users and passwords | `-L users.txt -P passlist.txt` |
| All combinations                | `-L users.txt -P passlist.txt` |
| Username = Password             | `-L users.txt -e s`            |

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

| Tip                                          | Description                              |
| -------------------------------------------- | ---------------------------------------- |
| Use `-t 4` to avoid service lockout          | Especially for SSH and SMB               |
| Use `-f` to stop on first success            | Saves time                               |
| Use `-V` or `-vV` to debug issues            | Helps track what's failing               |
| Try `-e nsr` to extend attack vectors        | Null, Same-as-username, Reverse-username |
| Pair with **proxychains** or VPN             | For stealth in engagements               |
| Use **custom http-post-form** logic          | Tailor to app-specific login formats     |
| Use **`hydra -U`** to show supported modules | Useful to discover new targets           |

---

### 💡 **Special Modules (as of 2025)**

| Module      | Use Case                        |
| ----------- | ------------------------------- |
| `http-head` | For checking headers-based auth |
| `imap`      | Mail brute-force                |
| `snmp`      | Community string brute-force    |
| `ldap2`     | Lightweight Directory Access    |
| `teamspeak` | Teamspeak 3 server login        |
| `asterisk`  | VoIP-based password brute-force |

---

### 🔐 **Output Handling**

| Option    | Action                               |
| --------- | ------------------------------------ |
| `-o FILE` | Save results to a file               |
| `-R`      | Resume session from `hydra.restore`  |
| `-I`      | Ignore errors that stop some modules |

---

### 🚀 **Combine with Other Tools**

| Tool          | Purpose                                 |
| ------------- | --------------------------------------- |
| `nmap`        | Identify open ports and services        |
| `cewl`        | Custom wordlist generation              |
| `burpsuite`   | Analyze HTTP login forms for post-form  |
| `crunch`      | Generate wordlists                      |
| `medusa`      | Alternative to Hydra with similar usage |
| `proxychains` | Add anonymity via proxy or TOR          |





To **combine `hydra` with the tools listed**, you can create a **powerful workflow for brute-forcing login credentials**, especially when targeting web applications or services behind authentication. Here's how each tool fits into the chain:

---

## 🔧 Combined Workflow Using Hydra & Others

| Tool              | How to Combine with `hydra`                                                                                                                                                                                                                                               |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **`nmap`**        | 🔍 First, scan the target to identify open ports and services. **Example:**`nmap -sV -Pn -T4 <target-ip>`→ Find login services like SSH, FTP, HTTP, RDP etc. Use the results to pick the correct `hydra` module.                                                          |
| **`cewl`**        | 📄 Generate a custom wordlist from target’s website. **Example:**`cewl http://target.com -w custom.txt`→ Use the wordlist as password input in Hydra:`hydra -L usernames.txt -P custom.txt target.com http-post-form "/login.php:user=^USER^&pass=^PASS^:F=Login Failed"` |
| **`burpsuite`**   | 🔎 Intercept login requests, analyze POST/GET structure, and extract proper form details for `hydra`. **Steps:**1. Capture login form2. Identify parameters (e.g., `username`, `password`)3. Note failure string4. Format for Hydra's `http-post-form`                    |
| **`crunch`**      | 🛠️ Generate large or pattern-specific wordlists. **Example:**`crunch 6 8 abc123 -o passlist.txt`→ Use with Hydra:`hydra -L users.txt -P passlist.txt ssh://<target-ip>`                                                                                                  |
| **`medusa`**      | 🔁 Can replace `hydra` if needed — try both for performance or compatibility. Some services may behave better with one.                                                                                                                                                   |
| **`proxychains`** | 🕵️‍♂️ Run `hydra` anonymously through TOR or proxies. **Example:**`proxychains hydra -L users.txt -P passwords.txt target.com ssh` → Ensure `/etc/proxychains.conf` is set to use TOR or SOCKS5 proxy.                                                                   |

---

## 🔄 Full Brute-Force Workflow Example

```bash
# 1. Discover open services
nmap -sV -Pn -T4 192.168.1.100

# 2. Scrape target site for words
cewl http://192.168.1.100 > cewl_list.txt

# 3. Capture and analyze HTTP login form
#    Use BurpSuite to extract POST format and failure string

# 4. Generate additional passwords if needed
crunch 6 8 -o pattern_list.txt

# 5. Run hydra with custom or generated wordlists
hydra -L users.txt -P cewl_list.txt 192.168.1.100 ssh

# OR for web login
hydra 192.168.1.100 http-post-form "/login:user=^USER^&pass=^PASS^:F=invalid"

# 6. Add anonymity
proxychains hydra -L users.txt -P pass.txt target.com ftp
```

---

## ✅ Tips

- Always check **rate limits** and **lockout protections**.
    
- Use `-t 4` or `-V` in Hydra to control speed/verbosity.
    
- `cewl` is powerful on **LinkedIn profiles, blogs**, or **employee pages**.
    
- Use **BurpSuite repeater** to fine-tune login failure responses.
    
- With `proxychains`, test latency to ensure TOR isn’t slowing attacks too much.
    

---



Would you like this in **PDF format** or added as a **markdown file** to share or keep offline?



NOTE!!!!!!!!!!! 
in lines like this -> "/login.php:user=^USER^&pass=^PASS^:Invalid credentials"

user and pass must match html file or they will never work 
most html files use username and password instead of user and pass 



# POSTGRESQL
hydra -L <userlist> -P <passlist> -s <port> -f <target> postgres
