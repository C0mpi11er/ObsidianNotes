
# 

> [!info] What is Windows Authentication?  
> Windows authentication is the process Windows uses to:
> 
> - Verify user identity
>     
> - Allow/deny login access
>     
> - Assign permissions
>     
> - Generate security logs
>     

---

## Core Components

### WinLogon (`winlogon.exe`)

> [!important] Role  
> Manages secure user logon interactions.

**Responsibilities:**

- Launches LogonUI
    
- Handles:
    
    - Login screen
        
    - Password changes
        
    - Lock/unlock workstation
        
- Passes credentials to LSASS
    

**Key fact:**  
It is the **only process** that receives secure login input directly from keyboard events.

---

### LogonUI (`logonui.exe`)

> [!note] Purpose  
> Displays the graphical login interface.

It:

- Presents login screen
    
- Collects credentials
    
- Uses Credential Providers to gather user input
    

---

### Credential Providers

> [!tip]  
> These are DLL-based COM objects that collect credentials.

Examples:

- Username/password box
    
- Smart card login
    
- Biometrics (Windows Hello)
    

They send collected credentials to WinLogon.

---

## LSASS (`lsass.exe`)

> [!danger] Critical Security Process  
> LSASS is the heart of Windows authentication.

**Location:**

```text
%SystemRoot%\System32\lsass.exe
```

### Responsibilities

- Authenticates users
    
- Enforces local security policy
    
- Manages security tokens
    
- Performs access checks
    
- Writes security audit logs
    
- Communicates with authentication packages
    

---

## Authentication Flow

> [!example] Login Sequence

```text
User enters credentials
    ↓
LogonUI collects them
    ↓
WinLogon receives them
    ↓
LSASS processes request
    ↓
Authentication package validates
    ↓
SAM or Active Directory verifies
    ↓
Access granted / denied
```

---

# Authentication Packages

## `Lsasrv.dll`

> [!info]  
> Main LSA server service

Functions:

- Enforces security policy
    
- Manages authentication packages
    
- Runs **Negotiate**
    

**Negotiate chooses:**

- NTLM
    
- Kerberos
    

---

## `Msv1_0.dll`

> [!note]  
> Handles local authentication

Used for:

- Local machine logons
    
- Non-domain logins
    

---

## `Kerberos.dll`

> [!important]  
> Handles Kerberos authentication

Used for:

- Domain authentication
    
- Ticket-based authentication
    

---

## `Samsrv.dll`

> [!note]  
> Manages Security Accounts Manager

Responsibilities:

- Stores local accounts
    
- Enforces local account policies
    

---

## `Netlogon.dll`

> [!tip]  
> Handles network-based logon requests

Used for:

- Domain communication
    
- Authentication with Domain Controllers
    

---

## `Ntdsa.dll`

> [!warning]  
> Only on Domain Controllers

Handles:

- Active Directory database
    
- LDAP requests
    
- Replication
    

---

# SAM Database

## What is SAM?

> [!info]  
> Security Account Manager stores local user credentials.

Location:

```text
%SystemRoot%\System32\config\SAM
```

Registry:

```text
HKLM\SAM
```

Contains:

- Usernames
    
- Password hashes
    
- Local security policies
    

---

## Access Requirements

> [!danger]  
> Requires SYSTEM privileges

Normal administrators cannot directly access it.

---

## Stored Hash Types

Windows stores:

- **LM Hash**
    
- **NTLM Hash**
    

---

# SYSKEY

> [!important]  
> Added to protect SAM against offline attacks

Purpose:

- Encrypt SAM database
    
- Protect password hashes on disk
    

---

# Credential Manager

## Purpose

> [!note]  
> Stores saved credentials for:

- Websites
    
- Network shares
    
- Applications
    
- Remote services
    

---

## Storage Location

```powershell
C:\Users\[Username]\AppData\Local\Microsoft\Vault
```

or

```powershell
C:\Users\[Username]\AppData\Local\Microsoft\Credentials
```

---

## Why It Matters

> [!warning]  
> Stored credentials may be decryptable during post-exploitation

Attackers often target Credential Manager.

---

# Active Directory & NTDS.dit

## NTDS.dit

> [!danger]  
> The Active Directory database

Location:

```text
%SystemRoot%\NTDS\ntds.dit
```

---

## Stores

- Domain user accounts
    
- Password hashes
    
- Group memberships
    
- Computer accounts
    
- Group Policy Objects
    

---

## Domain Authentication Flow

> [!example]

```text
User logs in
   ↓
Credentials sent to Domain Controller
   ↓
Kerberos validates against NTDS.dit
   ↓
Access granted
```

---

# Workgroup vs Domain

## Workgroup

> [!note]  
> Authentication is local

Uses:

```text
SAM
```

---

## Domain

> [!important]  
> Authentication is centralized

Uses:

```text
NTDS.dit on Domain Controller
```

---

# Pentesting Relevance

> [!danger] Why This Matters in Security Assessments

These components are common credential targets:

**LSASS**

- Memory dumping
    

**SAM**

- Hash extraction
    

**Credential Manager**

- Saved credential theft
    

**NTDS.dit**

- Domain credential extraction
    

---

# Quick Memory Trick

> [!tip] Remember

**Local authentication**

```text
SAM + Msv1_0
```

**Domain authentication**

```text
Kerberos + NTDS.dit
```

**Main controller**

```text
LSASS
```