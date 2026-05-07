# 🧠 SMB (Server Message Block)

---

> [!info] 📌 Overview
> SMB is a **client-server communication protocol** used for:
>
> * File sharing
> * Directory sharing
> * Printer sharing
> * Accessing network resources
>
> Mainly used in **Windows environments**, but Linux/Unix systems use **Samba** for SMB support.

---

# 🌐 SMB Ports

| Port | Service                  |
| ---- | ------------------------ |
| 137  | NetBIOS Name Service     |
| 138  | NetBIOS Datagram Service |
| 139  | NetBIOS Session Service  |
| 445  | SMB over TCP             |

---

# 🧠 SMB Communication Process

1. Client sends request.
2. TCP three-way handshake occurs.
3. SMB server processes request.
4. Data transferred between systems.

---

# 🧠 Access Control

> [!important] SMB uses ACLs (Access Control Lists)
>
> Permissions include:
>
> * Read
> * Write
> * Execute
> * Full control

Permissions can be assigned to:

* Users
* Groups

---

# 🧠 Samba

> [!info] Samba
> Samba is the Linux/Unix implementation of SMB.
>
> It enables:
>
> * Windows ↔ Linux communication
> * File sharing across platforms
> * SMB/CIFS functionality

---

# 🧠 SMB Versions

| Version     | Features                          |
| ----------- | --------------------------------- |
| CIFS / SMB1 | NetBIOS communication             |
| SMB 2.0     | Better performance                |
| SMB 2.1     | Improved locking                  |
| SMB 3.0     | Encryption + multichannel         |
| SMB 3.1.1   | Integrity checks + AES encryption |

---

# 🧠 Important Samba Daemons

| Daemon | Function             |
| ------ | -------------------- |
| smbd   | File/printer sharing |
| nmbd   | NetBIOS services     |

---

# 🧠 NetBIOS & Workgroups

> [!info]
>
> * Workgroup = Collection of systems/resources
> * NetBIOS = Naming + communication system
> * NBNS/WINS = Hostname registration & resolution

---

# 🧠 Default Samba Configuration

## Config File

```bash
/etc/samba/smb.conf
```

---

# 🧠 Important Samba Settings

| Setting        | Description            |
| -------------- | ---------------------- |
| workgroup      | SMB workgroup/domain   |
| path           | Shared folder          |
| browseable     | Show share publicly    |
| guest ok       | Allow anonymous access |
| read only      | Disable write access   |
| writable       | Allow modifications    |
| create mask    | File permissions       |
| directory mask | Directory permissions  |

---

# ⚠️ Dangerous Samba Settings

| Setting            | Risk                        |
| ------------------ | --------------------------- |
| guest ok = yes     | Anonymous access            |
| writable = yes     | File modification           |
| browseable = yes   | Easy share discovery        |
| create mask = 0777 | Full permissions            |
| magic script       | Potential command execution |

---

# 🧠 Example Vulnerable Share

```ini
[notes]
path = /mnt/notes/

browseable = yes
read only = no
writable = yes
guest ok = yes

create mask = 0777
directory mask = 0777
```

> [!warning]
> This configuration allows:
>
> * Anonymous access
> * File uploads
> * File modification
> * Full directory browsing

---

# 🧠 Restart Samba Service

```bash
sudo systemctl restart smbd
```

---

# 🧠 SMB Enumeration

## List Shares

```bash
smbclient -N -L //10.129.14.128
```

| Flag | Meaning      |
| ---- | ------------ |
| -N   | Null session |
| -L   | List shares  |

---

# 🧠 Connect to Share

```bash
smbclient //10.129.14.128/notes
```

---

# 🧠 Useful smbclient Commands

| Command | Function         |
| ------- | ---------------- |
| ls      | List files       |
| cd      | Change directory |
| get     | Download file    |
| put     | Upload file      |
| mkdir   | Create folder    |
| rm      | Delete file      |
| help    | Show commands    |

---

# 🧠 Download File

```bash
get prep-prod.txt
```

---

# 🧠 Execute Local Commands

```bash
!ls
!cat prep-prod.txt
```

---

# 🧠 smbstatus

> [!info]
> Shows:
>
> * Active SMB sessions
> * Connected users
> * Shares in use
> * SMB versions

```bash
smbstatus
```

---

# 🧠 Nmap SMB Enumeration

## Basic SMB Scan

```bash
sudo nmap -sV -sC -p139,445 10.129.14.128
```

---

# 🧠 Useful SMB NSE Information

| NSE Script         | Purpose          |
| ------------------ | ---------------- |
| smb2-security-mode | SMB signing info |
| smb2-time          | Server time      |
| nbstat             | NetBIOS details  |

---

# 🧠 RPCclient

> [!info]
> Used for MS-RPC enumeration on SMB services.

---

# 🧠 Connect Using Null Session

```bash
rpcclient -U "" 10.129.14.128
```

---

# 🧠 Useful rpcclient Commands

| Command         | Description        |
| --------------- | ------------------ |
| srvinfo         | Server information |
| enumdomains     | List domains       |
| querydominfo    | Domain details     |
| netshareenumall | Enumerate shares   |
| enumdomusers    | Enumerate users    |
| queryuser RID   | User details       |

---

# 🧠 Enumerate Shares

```bash
rpcclient $> netshareenumall
```

---

# 🧠 Enumerate Users

```bash
rpcclient $> enumdomusers
```

---

# 🧠 Query User Information

```bash
rpcclient $> queryuser 0x3e9
```

---

# 🧠 RID Brute Forcing

```bash
for i in $(seq 500 1100);do
rpcclient -N -U "" 10.129.14.128 \
-c "queryuser 0x$(printf '%x\n' $i)"
done
```

> [!important]
> Used to discover users by brute-forcing RIDs.

---

# 🧠 Samrdump.py

## Enumerate Users & Policies

```bash
samrdump.py 10.129.14.128
```

---

# 🧠 SMBMap

## Check Share Permissions

```bash
smbmap -H 10.129.14.128
```

---

# 🧠 CrackMapExec

## Enumerate SMB Shares

```bash
crackmapexec smb 10.129.14.128 --shares -u '' -p ''
```

> [!info]
> Shows:
>
> * SMB version
> * Signing status
> * Share permissions
> * READ/WRITE access

---

# 🧠 Enum4Linux-ng

## Installation

```bash
git clone https://github.com/cddmp/enum4linux-ng.git

cd enum4linux-ng

pip3 install -r requirements.txt
```

---

# 🧠 Full Enumeration

```bash
./enum4linux-ng.py 10.129.14.128 -A
```

---

# 🧠 Information Gathered

* SMB versions
* Null session access
* Shares
* Users
* Password policies
* OS version
* SMB signing

---

# ⚠️ Common SMB Risks

| Risk            | Impact                |
| --------------- | --------------------- |
| Null sessions   | Anonymous enumeration |
| Writable shares | Malicious uploads     |
| SMBv1 enabled   | Legacy exploits       |
| Weak ACLs       | Unauthorized access   |
| No SMB signing  | Relay attacks         |

---

# 🧠 Important Enumeration Commands

## SMBclient

```bash
smbclient -N -L //IP
smbclient //IP/share
```

## Nmap

```bash
nmap -sV -sC -p139,445 IP
```

## RPCclient

```bash
rpcclient -U "" IP
```

## SMBMap

```bash
smbmap -H IP
```

## CrackMapExec

```bash
crackmapexec smb IP --shares -u '' -p ''
```

## Enum4Linux-ng

```bash
./enum4linux-ng.py IP -A
```

---

# 🧠 Key Takeaways

> [!summary]
>
> * SMB is heavily used in internal Windows networks.
> * Samba enables SMB support on Linux systems.
> * Misconfigured shares can expose sensitive information.
> * Enumeration helps discover:
>
>   * Shares
>   * Users
>   * Permissions
>   * Password policies
> * Always combine multiple enumeration tools.
