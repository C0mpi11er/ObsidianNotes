# 🧠 Virtual Host (VHost) Enumeration Cheat Sheet

---

> [!info] 📌 Overview  
> **Virtual Hosts (VHosts)** allow multiple websites/apps to run on the same IP.

Difference:

|Type|Meaning|
|---|---|
|**Subdomain**|DNS record exists (`dev.example.com`)|
|**VHost**|Web server config responds to Host header|

A VHost may exist **without public DNS records**.

That’s why VHost fuzzing matters.

---

# 🔍 Basic VHost Enumeration

Using **Gobuster**

```bash
gobuster vhost -u http://TARGET_IP -w wordlist.txt --append-domain
```

---

# 🌐 Example

```bash
gobuster vhost -u http://10.10.10.10 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt --append-domain
```

---

# 🎯 HTB Style

```bash
gobuster vhost -u http://inlanefreight.htb:81 -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt --append-domain
```

---

# ⚡ Faster Scan (More Threads)

```bash
gobuster vhost -u http://TARGET_IP -w wordlist.txt --append-domain -t 50
```

---

# 🔒 Ignore SSL Errors

For HTTPS targets:

```bash
gobuster vhost -u https://TARGET_IP -w wordlist.txt --append-domain -k
```

---

# 💾 Save Results

```bash
gobuster vhost -u http://TARGET_IP -w wordlist.txt --append-domain -o results.txt
```

---

# 🔍 Filter by Response Size

Very useful for removing noise.

```bash
gobuster vhost -u http://TARGET_IP -w wordlist.txt --append-domain --exclude-length 1234
```

First check default response size.

---

# 🧠 Check Baseline Response

```bash
curl -H "Host: random.example.com" http://TARGET_IP
```

Compare:

```bash
curl -H "Host: admin.example.com" http://TARGET_IP
```

---

# 🚀 Using **ffuf**

## Basic Host Header Fuzzing

```bash
ffuf -u http://TARGET_IP -H "Host: FUZZ.example.com" -w wordlist.txt
```

---

## Filter by Size

```bash
ffuf -u http://TARGET_IP -H "Host: FUZZ.example.com" -w wordlist.txt -fs 1234
```

---

## Match Status Code

```bash
ffuf -u http://TARGET_IP -H "Host: FUZZ.example.com" -w wordlist.txt -mc 200,302,403
```

---

# ⚡ Using **Feroxbuster**

```bash
feroxbuster -u http://TARGET_IP -H "Host: FUZZ.example.com"
```

Less common for VHosts, but works.

---

# 🧪 Manual Host Header Testing

Test if VHost exists:

```bash
curl -H "Host: admin.example.com" http://TARGET_IP
```

---

Using browser:

Add to `/etc/hosts`

```bash
10.10.10.10 admin.example.com
```

Then browse:

```text
http://admin.example.com
```

---

# 📂 Good Wordlists

From **SecLists**

## Small

```bash
Discovery/DNS/subdomains-top1million-5000.txt
```

---

## Medium

```bash
Discovery/DNS/subdomains-top1million-20000.txt
```

---

## Large

```bash
Discovery/DNS/subdomains-top1million-110000.txt
```

---

# 🎯 Real Workflow

## 1. Resolve target IP

```bash
dig example.com +short
```

---

## 2. Baseline response

```bash
curl -H "Host: fake.example.com" http://TARGET_IP
```

---

## 3. Run Gobuster

```bash
gobuster vhost -u http://TARGET_IP -w wordlist.txt --append-domain
```

---

## 4. Add found host

```bash
echo "TARGET_IP admin.example.com" | sudo tee -a /etc/hosts
```

---

## 5. Browse / enumerate

```bash
firefox http://admin.example.com
```

---

# ⚠️ Common Pitfalls

|Problem|Cause|
|---|---|
|All responses match|wildcard/default vhost|
|No hits|wrong base domain|
|TLS errors|missing `-k`|
|False positives|response size not filtered|

---

# 🔥 Most Used Commands

```bash
gobuster vhost -u http://TARGET_IP -w wordlist.txt --append-domain
```

```bash
ffuf -u http://TARGET_IP -H "Host: FUZZ.example.com" -w wordlist.txt
```

```bash
curl -H "Host: admin.example.com" http://TARGET_IP
```

---

# 🧠 Key Takeaways

> [!summary]
> 
> - VHosts are web-server level
>     
> - They may exist without DNS
>     
> - Host header fuzzing reveals hidden apps
>     
> - Gobuster + ffuf are the go-to tools
>     
> - Always filter false positives by response size
>     

---

This is one of those recon steps people skip, and then wonder why they missed the hidden admin panel sitting quietly on the same IP.