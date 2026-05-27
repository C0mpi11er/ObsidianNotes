## Credential Hunting in Network Traffic — Cheat Sheet

> [!ABSTRACT] What it is  
> Hunt **cleartext credentials** inside packet captures using **Wireshark** or **Pcredz**.

Targets:

- HTTP POST creds
    
- FTP logins
    
- SNMP community strings
    
- Email protocol creds
    
- NTLM / Kerberos hashes
    

---

# > [!INFO] Open Capture

## Wireshark

```bash
wireshark demo.pcapng
```

## Quick strings check

```bash
strings demo.pcapng | less
```

---

# > [!SUCCESS] Wireshark Filters

## HTTP traffic

```wireshark
http
```

---

## POST requests (high value)

```wireshark
http.request.method == "POST"
```

---

## Search passwords

```wireshark
http contains "pass"
```

Better:

```wireshark
frame contains "password"
```

---

## FTP credentials

```wireshark
ftp
```

Look for:

```text
USER
PASS
```

---

## SNMP community strings

```wireshark
snmp
```

Common:

- public
    
- private
    

---

## Mail protocols

```wireshark
pop || imap || smtp
```

Look for:

```text
USER
PASS
LOGIN
AUTH
```

---

## HTTP Basic Auth

```wireshark
http.authorization
```

Decode:

```bash
echo '<base64>' | base64 -d
```

---

# > [!INFO] Follow Full Conversations

## Follow TCP stream

```wireshark
tcp.stream eq X
```

Best way to reconstruct creds.

GUI:

**Right Click → Follow → TCP Stream**

---

# > [!SUCCESS] Pcredz

## Run against PCAP

```bash
./Pcredz -f demo.pcapng -t -v
```

Extracts:

- HTTP creds
    
- FTP creds
    
- SNMP strings
    
- NTLM hashes
    
- Kerberos hashes
    

---

# > [!CHECK] Fast Search Order

## 1. Automated extraction

```bash
./Pcredz -f demo.pcapng -t -v
```

---

## 2. Hunt POST forms

```wireshark
http.request.method == "POST"
```

---

## 3. Search password strings

```wireshark
frame contains "pass"
```

---

## 4. Check plaintext protocols

```wireshark
ftp || snmp || pop || imap || smtp
```

---

## 5. Follow suspicious streams

```wireshark
tcp.stream eq X
```

---

# > [!WARNING] Highest Yield Filters

```wireshark
http contains "pass"
frame contains "password"
http.request.method == "POST"
ftp
snmp
http.authorization
```

---

# > [!TLDR] HTB Default Workflow

## Scan automatically

```bash
./Pcredz -f demo.pcapng -t -v
```

## Manual credential hunt

```wireshark
http.request.method == "POST"
```

## Search strings

```wireshark
frame contains "pass"
```

## Reconstruct session

```wireshark
tcp.stream eq X
```