


---

## 🧠 Credential Harvesting on Windows Systems

Credential gathering is one of the **easiest and most effective ways** to gain further access once a Windows machine is compromised. This note covers common locations where credentials may be stored or leaked.

---

## 🪪 User Credentials in Plaintext or Config Files

Many users unintentionally leave credentials behind in:

* **Plaintext files**
* **Configuration files**
* **Saved software settings**

Once compromised, a machine can yield **cleartext usernames and passwords** with minimal effort.

---

## 📂 Unattended Windows Installations

System administrators may use **Windows Deployment Services** to automate mass installations. These **"unattended" setups** often contain administrator credentials in config files.

### 🧾 Possible Locations:

```text
C:\Unattend.xml
C:\Windows\Panther\Unattend.xml
C:\Windows\Panther\Unattend\Unattend.xml
C:\Windows\system32\sysprep.inf
C:\Windows\system32\sysprep\sysprep.xml
```

### 🔐 Example Credential Snippet:

```xml
<Credentials>
    <Username>Administrator</Username>
    <Domain>thm.local</Domain>
    <Password>MyPassword123</Password>
</Credentials>
```

> 💡 These files are readable by low-privilege users on some misconfigured systems.

---

## 🧾 PowerShell History

Every PowerShell command run by a user is saved in a **history file**.

If a password was entered directly into PowerShell (e.g., passed as a string to a script), it can be recovered from:

### 📄 History File:

```bash
%userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```

### 🔍 From CMD:

```cmd
type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```

### 🔍 From PowerShell:

```powershell
Get-Content "$Env:USERPROFILE\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt"
```

---

## 💾 Saved Windows Credentials

Windows supports storing credentials for **remote access** and **network shares**.

### 📋 List Saved Credentials:

```cmd
cmdkey /list
```

### ▶️ Reuse with RunAs (no password prompt):

```cmd
runas /savecred /user:admin cmd.exe
```

> 🛑 You can’t extract the plaintext password, but if `savecred` was used, you can piggyback on it.

---

## 🌐 IIS Configuration (web.config)

**IIS** (Internet Information Services) is the built-in Windows web server. Credentials may be stored in `web.config`, typically used for:

* **Database connection strings**
* **Basic authentication config**

### 🔍 Common Paths:

```text
C:\inetpub\wwwroot\web.config
C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config
```

### 🔍 Quick Search for Connection Strings:

```cmd
type C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\web.config | findstr connectionString
```

---

## 📡 Retrieve Credentials from PuTTY

**PuTTY** is a widely-used SSH client for Windows.

* It **does not store** SSH passwords.
* But it **can store proxy usernames and passwords in cleartext**.

### 🔍 Location in Windows Registry:

```cmd
reg query HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions\ /f "Proxy" /s
```

> ✅ Look for `ProxyUsername` and `ProxyPassword` values under saved sessions.

---

## 🧠 Pro Tip: Don’t Stop at PuTTY

Other apps often save credentials too:

* 🧭 Browsers (Chrome, Firefox, Edge)
* 📧 Email clients (Outlook, Thunderbird)
* 🗃️ FTP clients (FileZilla, WinSCP)
* 🖥️ Remote desktop tools (VNC, RDP managers)
* 🔐 Password managers (especially if unlocked)

Tools like **Mimikatz**, **LaZagne**, or even `reg query` and `findstr` can help dump those as well.

---

## Other Techniuqes
[[Windows Priv Esc(Sheduled task)]]
[[🔒 Privilege Escalation via `SeTakeOwnershipPrivilege` & `utilman.exe` Abuse]]
[[Windows Priv SEBackup,SERestore]]
# Privileges 
[[Type Of Windows Privileges]]

# Exploiting Those Priv 
https://github.com/gtworek/Priv2Admin 


