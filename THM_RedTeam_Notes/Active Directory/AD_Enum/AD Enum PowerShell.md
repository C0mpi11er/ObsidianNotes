AD powershell commands 

https://learn.microsoft.com/en-us/powershell/module/activedirectory/?view=windowsserver2022-ps


## ⚡ Active Directory: Enumeration via PowerShell Cmdlets

### 💡 Overview

**PowerShell** is an object-oriented shell that is the successor to Command Prompt. It uses specialized **cmdlets** (pronounced command-lets, which are .NET classes) to perform functions, offering significant advantages over basic `net` commands.

- **Requirement:** Requires the **AD Remote Server Administration Tools (RSAT)** cmdlets to be installed (often installed alongside the MMC Snap-Ins).
    
- **Execution:** Commands are executed from a `powershell` session launched within the **`runas.exe /netonly`** command prompt, allowing a non-domain-joined machine to authenticate using injected AD credentials.
    
- **Key Parameters:** Most AD cmdlets accept **`-Server`** (to specify the Domain Controller) and **`-Filter`** (for granular search capabilities).
    

---

### 🔍 Core Enumeration Cmdlets

| Cmdlet                  | Purpose                                         | Syntax/Example                                                                      | Key Takeaway                                                                                         |
| ----------------------- | ----------------------------------------------- | ----------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| **`Get-ADUser`**        | Retrieve **detailed user properties**.          | `Get-ADUser -Identity gordon.stevens -Properties * -Server <DC>`                    | **`-Properties *`** retrieves **_all_** attributes, overcoming the limits of `net user`.             |
|                         | **Filtering**                                   | `Get-ADUser -Filter 'Name -like "*stevens"' \| Format-Table Name,SamAccountName -A` | Allows complex filtering and piping to `Format-Table` for clean output.                              |
| **`Get-ADGroup`**       | Retrieve group details.                         | `Get-ADGroup -Identity Administrators -Server <DC>`                                 | Provides details like **GroupScope**, **GroupCategory**, and **SID**.                                |
| **`Get-ADGroupMember`** | List **all members** of a specific group.       | `Get-ADGroupMember -Identity Administrators -Server <DC>`                           | **Reliably** retrieves _all_ members, unlike the unreliable `net group` command.                     |
| **`Get-ADDomain`**      | Retrieve **domain-wide configuration** details. | `Get-ADDomain -Server <DC>`                                                         | Lists containers (`UsersContainer`, `DomainControllersContainer`) and domain properties (`DNSRoot`). |

