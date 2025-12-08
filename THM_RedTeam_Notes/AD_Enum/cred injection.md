# 🛡️ Active Directory: Credential Injection via Runas.exe

## 💡 Overview
Method to leverage discovered **Active Directory (AD) credentials** from a **non-domain-joined Windows machine** for enumeration and exploitation. Essential for in-depth AD assessments where a domain-joined host is unavailable.

## 🔑 Runas.exe /netonly Explained
`runas.exe` is a built-in Windows binary. The `/netonly` parameter is the key to credential injection.

* **Syntax:**
    ```bash
    runas.exe /netonly /user:<FQDN>\<username> cmd.exe
    ```
* **`/netonly` Function:**
    * **Local:** Commands run under the current local user context.
    * **Network:** **All network-related commands/applications** launched from the spawned `cmd.exe` session will use the **injected AD credentials** for authentication.
* **FQDN:** Use the **Fully Qualified Domain Name** (e.g., `za.tryhackme.com`) instead of the NetBIOS name for reliable resolution.
* **Password Prompt:** The utility will prompt for the password. Since `/netonly` is used, the password is **not verified** against a DC at this step.

## 🛠️ Verification Steps

### 1. Configure DNS (If needed)
* Must ensure your machine can resolve the AD domain. The safest DNS is the Domain Controller (DC) IP.
* **PowerShell Command:**
    ```powershell
    $dnsip = "<DC IP>"
    $index = Get-NetAdapter -Name 'Ethernet' | Select-Object -ExpandProperty 'ifIndex'
    Set-DnsClientServerAddress -InterfaceIndex $index -ServerAddresses $dnsip
    ```
* **Verification:** `nslookup <domain FQDN>` (e.g., `nslookup za.tryhackme.com`) should resolve to the DC IP.

### 2. Test Credential Success (SYSVOL)
The most reliable test is to access the **SYSVOL** share, which all AD users can read.

* **SYSVOL:** Shared folder on all DCs storing **Group Policy Objects (GPOs)** and domain scripts.
* **Verification Command:**
    ```bash
    dir \\<domain FQDN>\SYSVOL\
    ```
    *A successful listing confirms the credentials work for network access.*

## 👻 Authentication Stealth (IP vs. Hostname)
The identifier used affects the network authentication protocol:

| Identifier     | Authentication | Purpose                                                                                                         |
| :------------- | :------------- | :-------------------------------------------------------------------------------------------------------------- |
| **Hostname**   | **Kerberos**   | Standard AD protocol.                                                                                           |
| **IP Address** | **NTLM**       | Forces NTLM. Good for stealth as it can evade monitoring focused on Kerberos attacks (e.g., OverPass-The-Hash). |

## 🚀 Application Exploitation
Any application launched from the `runas`-spawned command prompt uses the injected credentials for network connections:

* **MS SQL:** Launching SQL Studio allows you to authenticate to an MS SQL database using **Windows Authentication** with the injected AD credentials, even without being domain-joined.
* **Web Apps:** Authenticate to web applications using **NTLM Authentication**.