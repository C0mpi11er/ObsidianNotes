
## Lecture Summary: Windows File Transfer Operations




### Core Concepts

>[!INFO] **Fileless Threats**

- **Definition:** Attacks that use legitimate, built-in system tools (Living Off The Land) to execute malicious actions.
    
- **Mechanism:** Payloads often run directly in **RAM (Memory)** rather than being written to the physical disk, making them harder to detect via traditional antivirus.
    
- **Example (Astaroth Attack):** Uses a chain of `WMIC` -> `Bitsadmin` -> `Certutil` -> `Regsvr32` to download and load malware without creating obvious suspicious files.
    

>[!INFO] **Integrity Verification (MD5)**

- Always use checksums (like `md5sum` on Linux or `Get-FileHash` on Windows) to ensure files are transferred correctly and haven't been corrupted or tampered with.
    

---

### Key Download Strategies

- **PowerShell Base64:** Best for small files when network communication is restricted. Convert the file to a string, copy it, and decode it on the target.
    
- **PowerShell WebClient:** Uses the `System.Net.WebClient` class.
    
    - `DownloadFile`: Saves directly to disk.
        
    - `DownloadString`: Allows for **fileless** execution by piping output to `IEX`.
        
- **SMB (Server Message Block):** Common in enterprise environments. Uses Port 445. Can be bypassed using **WebDAV** (SMB over HTTP) if 445 is blocked.
    
- **FTP (File Transfer Protocol):** Useful for non-interactive shells by using "command files" to automate the login and transfer.
    

---

### Key Upload Strategies

- **Exfiltration:** Moving data (hashes, sensitive docs) from the target to the attack machine.
    
- **Web Uploads:** Requires a specialized server (like Python's `uploadserver`) because standard web servers don't usually allow anonymous `POST` uploads by default.
    
- **Base64 Web Uploads:** Sending encoded strings via `Invoke-WebRequest` and catching them with `Netcat`.
    

---

### Common Bypasses for Defenders

- **`-UseBasicParsing`:** Essential when Internet Explorer hasn't been initialized on a new Windows install.
    
- **SSL/TLS Validation:** A one-liner script is used to force PowerShell to trust self-signed or invalid certificates from your attack machine.
    

