# 🔐 IPMI Hash Disclosure

> [!ABSTRACT] Overview of IPMI Hash Disclosure
>
> This note covers how to detect and exploit an insecure configuration that allows for disclosure of IPMI hashes, leading to potential unauthorized access to system management interfaces.

---

## 🕵️ Initial Detection

### Checking for Open Ports

```bash
nmap -p 623 <TARGET_IP>
```

### Confirming IPMI Access with `ipmitool`

Anonymous login:

```bash
ipmitool -I lanplus -H <IP> -U root user list
```

Authenticated login:

```text
ipmitool -I lanplus -H <IP> -U admin -P password channel info 1
```

---

## 🚨 Exploiting IPMI Hash Disclosure

### Retrieving the IPMI Configuration

Anonymous login attempt:

```bash
ipmitool -I lanplus -H <TARGET_IP> user list
```

If successful, it indicates the system is vulnerable to IPMI hash disclosure.

### Gathering the Hashes

Use `ipmitool` with anonymous access or a weak account if one exists:

```bash
ipmitool -I lanplus -H <IP> -U admin -P password user get 1
```

The response will provide information about the user, including potentially the IPMI hash.

---

## 🔒 Mitigations

### Secure Configuration

- Ensure strong passwords for all users.
- Disable unnecessary services and limit network access to only trusted networks.

### Best Practices

> [!NOTE] Important Security Practices
>
> Regularly audit system configurations and enable secure settings like encryption and authentication mechanisms provided by IPMI standards.

---

## 🛑 Common Findings

| Vulnerability | Impact |
|---|---|
| Open IPMI Port | Unauthorized Access to System Management Interface |
| Weak Credentials | Elevation of Privilege |

---

> [!WARNING] Important Warning
>
> Disclosing or using IPMI hashes without authorization can lead to serious security breaches and legal issues.

---

## 🧠 Exam Mental Model

```text
Open 623?
   ↓
Anonymous Access?
   ↓
Hash Disclosure?
   ↓
Secure Config?
```

Whenever you detect port 623 open, immediately check for anonymous access vulnerabilities that could lead to IPMI hash disclosure.