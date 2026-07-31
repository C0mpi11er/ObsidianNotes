# 🔐 Credentials Tracking

---

> [!ABSTRACT] Summary of Inlanefreight Penetration Test Evidence and Notes on Credential Handling.

## 📂 Documentation & Reporting

### HTB Academy Example: Inlanefreight Penetration Test

#### 6. Credentials.md

---

> [!INFO] Overview
>
> This document details the steps taken to track and manage credentials during the penetration test of the Inlanefreight environment in the HTB Academy.

## 🎯 Initial Assessment & Enumeration

### Nmap Scan for Open Ports

```bash
sudo nmap -p- <target_ip>
```

Identifying open ports specifically related to credential handling:

```bash
sudo nmap -sV --script=credentials -p 21,22,80,443,3306 <target_ip>
```

### Credentials Discovery

#### Use of Tools and Techniques

##### Hydra for Brute-Force Attacks

Brute-forcing FTP credentials:

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt ftp://<target_ip>:21
```

SSH brute-force using a list of common usernames and passwords:

```bash
hydra -L users.txt -P password.txt ssh://<target_ip>
```

##### Medusa for More Complex Attacks

Brute-forcing MySQL credentials with multiple threads:

```bash
medusa -h <target_ip> -u admin -p "pass123" -M mysql -m database:information_schema -n 5
```

---

## 📂 Extracting Credentials from Web Applications

### Searching for Default and Hardcoded Credentials

#### Scanning HTTP Responses with Nikto

Identifying default credentials in web server responses:

```bash
nikto -h <target_ip> -C all
```

##### Using Burp Suite to Intercept and Analyze Traffic

Setting up an intercept point for HTTP requests:

1. Start Burp Suite.
2. Configure proxy settings in your browser (e.g., 127.0.0.1:8080).
3. Enable the "Intercept" feature in Burp Suite.

Analyze captured traffic to find credentials embedded in URLs or request bodies.

---

## 🎭 Exploiting Misconfigured Services

### RDP and SMB Credentials Management

#### Checking for Weak Password Policies via BloodHound

```bash
bloodhound --config-file=/path/to/config.yaml -u <domain/user> -p <password>
```

##### Lateral Movement with Pass-the-Hash (PtH)

Using Impacket to perform PtH attacks:

```python
impacket-secretsdump USER=<target_username> NTLM_HASH=<hash_value> TARGET=DomainController
```

---

## 📄 Logging and Reporting

### Documentation of Found Credentials

#### Creating a Structured Log File

Documenting credentials in an organized log file for future reference:

```bash
echo "Target: <target_ip>" >> credentials.log
echo "Credential Type: FTP" >> credentials.log
echo "Username: admin" >> credentials.log
echo "Password: pass123" >> credentials.log
```

---

## 🧠 Mental Model

### Credential Tracking Workflow

- **Scan** for open ports and services.
- **Discover** potential credential vectors.
- **Extract** credentials from network traffic, web applications, and misconfigured services.
- **Log** all findings in a structured format.

> [!SUCCESS] Whenever you identify an insecure service or default login, immediately prioritize its remediation to mitigate risk.

---

## 📋 Common Findings

| Finding | Details |
|---|---|
| Weak Password Policies | Default passwords found on services. |
| Misconfigured Services | RDP and SMB with weak authentication methods. |
| Embedded Credentials | Hardcoded credentials in web application responses. |

> [!WARNING] Always ensure that all discovered credentials are handled securely to prevent accidental exposure.

---

## 🛠 Tools & Techniques

### Utilized Tools for Credential Tracking

- [[Hydra]]
- [[Medusa]]
- [[Nikto]]
- Burp Suite
- Impacket
- BloodHound