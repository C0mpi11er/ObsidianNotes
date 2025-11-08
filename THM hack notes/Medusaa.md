Here is the Medusa brute-forcing tool cheat sheet formatted for easy copy-and-paste into your Obsidian note.

---

## Medusa Brute-Forcing Tool Cheat Sheet

Medusa is a fast, parallel, modular, login brute-forcing tool.

### 1. Core Syntax & Essential Flags

|Flag|Purpose|Example Value|
|---|---|---|
|**`-h`**|Target **Host** (IP or hostname)|`10.10.10.5`|
|**`-H`**|File containing a list of **Hosts**|`targets.txt`|
|**`-u`**|Single **Username** to test|`jdoe`|
|**`-U`**|File containing a list of **Usernames**|`users.txt`|
|**`-p`**|Single **Password** to test|`secret123`|
|**`-P`**|File containing a list of **Passwords** (dictionary)|`rockyou.txt`|
|**`-C`**|File containing **User:Password** combinations|`combo.txt`|
|**`-M`**|**Module** (Protocol) to attack|`ssh` or `ftp`|
|**`-n`**|**Port** number (if not the default)|`2222`|
|**`-t`**|Total **Threads** (parallel connections)|`5`|
|**`-w`**|File to write **successful credentials**|`found.txt`|

---

### 2. Common Attack Modules (`-M`)

|Module|Protocol|Default Port|Notes|
|---|---|---|---|
|**`ssh`**|Secure Shell|22|Best for remote shell access.|
|**`ftp`**|File Transfer Protocol|21|Standard file sharing.|
|**`smtp`**|Simple Mail Transfer Protocol|25|Targets mail server authentication.|
|**`smb`**|Server Message Block|445|Windows file sharing.|
|**`telnet`**|Telnet|23|Unencrypted remote login.|
|**`vnc`**|Virtual Network Computing|5900|Remote desktop.|

---

### 3. Practical Command Examples

#### 1. SSH Dictionary Attack (Single User)

Bash

```
medusa -h 10.10.10.5 -u admin -P /usr/share/wordlists/rockyou.txt -M ssh -t 5
```

#### 2. FTP with User List and Password List

Bash

```
medusa -h 192.168.1.1 -U users.txt -P passwords.txt -M ftp -t 4
```

#### 3. SMTP Attack on a Custom Port (Submission Port)

Bash

```
medusa -u user@mail.thm -h 10.10.177.189 -P pass.txt -M smtp -n 587
```

#### 4. HTTP Basic Authentication (Often requires specific headers, but try this first)

Bash

```
medusa -h 10.0.0.1 -u webmaster -P web_passwords.txt -M http
```

---

### 4. Troubleshooting Specific Errors

| Error Message                | Likely Cause                                      | Recommended Fix                                                                |
| ---------------------------- | ------------------------------------------------- | ------------------------------------------------------------------------------ |
| **Failed to match regex...** | Server banner/response is non-standard.           | Try another port (`-n 587`) or use the **`-e n`** flag to skip error checking. |
| **Connection refused**       | Port is closed or blocked by a firewall.          | **Nmap** the target port to verify it's open: `nmap -p 22 10.10.10.5`          |
| **Slow or Times out**        | Aggressive threading is triggering rate-limiting. | Reduce threads: **`-t 1`** or **`-t 2`**.                                      |