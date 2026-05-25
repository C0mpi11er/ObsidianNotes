> [!info]  
> Windows Credential Manager (CredMan) is a **DPAPI-backed credential storage system** used by applications and users to persist passwords, tokens, and authentication material.

---

## 🗄️ 1. Where Credentials Are Stored

> [!note]  
> Credential Manager stores encrypted blobs inside **Vault / Credentials directories**.

### 📁 Common storage paths

```
%UserProfile%\AppData\Local\Microsoft\Vault\
%UserProfile%\AppData\Local\Microsoft\Credentials\
%UserProfile%\AppData\Roaming\Microsoft\Vault\
%ProgramData%\Microsoft\Vault\
%SystemRoot%\System32\config\systemprofile\AppData\Roaming\Microsoft\Vault\
```

---

> [!important]  
> These files are **not plaintext credentials** — they are:
> 
> - AES-encrypted blobs
> - protected by DPAPI keys
> - tied to user/system context

---

## 🔐 2. Credential Types in Windows Vault

|Type|Description|
|---|---|
|Web Credentials|Browser / legacy Edge / IE saved logins|
|Windows Credentials|SMB, RDP, domain logins, services, network shares|

---

> [!tip]  
> Think of it as:
> 
> - Web creds = browser passwords
> - Windows creds = system/network authentication tokens

---

## 🧠 3. Core Protection Mechanism

> [!warning]  
> Credential Manager security is layered:

1. Credentials are encrypted using **AES**
2. AES keys are stored in `Policy.vpol`
3. `Policy.vpol` is protected using **DPAPI**
4. DPAPI keys are tied to:
    - user SID
    - machine context

---

> [!important]  
> So the real dependency chain is:
> 
> ```
> Credential → AES → DPAPI → User/Machine Master Key
> ```

---

## 🔍 4. Enumerating Stored Credentials

### 📋 cmdkey enumeration

```
cmdkey /list
```

> [!info]  
> Shows stored credentials in the current user context.

Example output:

- Domain credentials
- Generic application credentials
- Interactive logon credentials

---

### 🧾 Interpreting output

> [!note]  
> Key fields:

- **Target** → what system/service the credential is for
- **Type** → Generic or Domain Password
- **User** → associated account
- **Persistence** → survives reboot or not

---

## 🚀 5. Credential Reuse Attack

> [!warning]  
> If you see:

```
Domain:interactive=DOMAIN\user
```

You can potentially reuse it via:

```
runas /savecred /user:DOMAIN\user cmd
```

---

> [!important]  
> This works because Windows may cache credentials for **re-authentication without prompting password again**.

---

## 🧬 6. Credential Extraction with Mimikatz

> [!danger]  
> Requires elevated privileges (debug privilege + LSASS access)

---

### 🔓 Extract Credential Manager secrets

```
sekurlsa::credman
```

---

> [!info]  
> This reads credentials from:

- LSASS memory
- Credential Manager cache
- active logon sessions

---

### Example output meaning

```
Username : mcharles@inlanefreight.localDomain   : onedrive.live.comPassword : <recovered>
```

---

> [!success]  
> This shows **real decrypted credentials**, not hashes.

---

## 🧠 7. Alternative Tools

> [!note]  
> Credential Manager attacks are not limited to Mimikatz.

### Useful tools:

- SharpDPAPI
- LaZagne
- DonPAPI
- Mimikatz (`credman`, `dpapi` modules)

---

## 🧩 8. Attack Chain (How it fits together)

> [!summary]  
> Typical workflow:

1. Gain local access
2. Enumerate saved credentials (`cmdkey /list`)
3. Extract secrets:
    - LSASS → live creds
    - DPAPI → decrypted vaults
4. Reuse credentials for:
    - lateral movement
    - privilege escalation

---

## 🧠 Mental Model

> [!tip]  
> Credential Manager ≈ “saved passwords layer on top of DPAPI”

- DPAPI = master encryption system
- Credential Manager = application using DPAPI
- LSASS = runtime memory cache of everything

---

If you want next step, I can connect this with:

- **DPAPI attack flow (masterkey → blob → plaintext)**
- or **how Credential Manager ties into browser password extraction**
- or build you a **full Windows credential dumping cheat sheet (SAM + LSASS + CredMan unified)**