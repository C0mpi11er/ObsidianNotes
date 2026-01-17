
# ⚡ Active Directory: Enumeration via Command Prompt (`net`)

## 💡 Overview
Using the native Windows `net` command with the `/domain` switch for quick, stealthy, and non-GUI AD reconnaissance. Ideal for use in limited shells (RATs) or initial phishing payloads.

## 🔑 Core Commands

| Command | Purpose | Output Details |
| :--- | :--- | :--- |
| `net user /domain` | List all AD user accounts. | Usernames only. Determines domain size. |
| `net user <user> /domain` | Detailed information on a specific user. | Password age, active status, group membership (often truncated if many groups). |
| `net group /domain` | List all Global/Local group accounts in AD. | Identifies high-value groups like 'Domain Admins'. |
| `net group "Group Name" /domain` | List all members of a specific group. | Direct identification of target users. |
| `net accounts /domain` | Enumerate the domain-wide password policy. | Lockout threshold/duration, min/max password age/length, and password history. |

## 🎯 Reconnaissance Value
The `net accounts /domain` output is critical for planning **password spraying** attacks, as it reveals:
* **Lockout Threshold:** Determines how many login attempts are safe.
* **Password Length/Age:** Narrows down password guessing pool.

## ⚖️ Benefits & Limitations

| Benefits 👍 | Limitations 👎 |
| :--- | :--- |
| **Stealth:** Built-in binary, less monitored than PowerShell/external tools. | **Domain-Join Required:** Must be executed on a **domain-joined machine** to query the AD. |
| **No GUI:** Works in any CMD shell or simple RAT. | **Limited Output:** Truncates long lists (e.g., group memberships > 10). |
| **VBScript/RAT Integration:** Easily executed programmatically. | **No Computer Enumeration:** Not suitable for listing all domain-joined computers. |