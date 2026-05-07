# 🧠 NFS (Network File System)

---

> [!info] 📌 Overview
> NFS is a **distributed file system protocol** developed by Sun Microsystems that allows users to:
>
> * Access remote files as if they are local
> * Share directories across Linux/Unix systems
> * Mount remote file systems over a network
>
> Unlike SMB, NFS is primarily used in **Linux/Unix environments**.

---

# 🌐 NFS Ports

| Port   | Service                                            |
| ------ | -------------------------------------------------- |
| 111    | rpcbind (portmapper)                               |
| 2049   | NFS service                                        |
| 20048+ | mountd / lockd / additional RPC services (dynamic) |

---

# 🧠 NFS Communication Process

1. Client queries **rpcbind (port 111)** to discover NFS services.
2. Client requests available exports via **mountd**.
3. Client mounts shared directory.
4. File operations occur over **NFS (port 2049)**.

---

# 🧠 NFS Protocol Stack

> [!info]
> NFS relies on:
>
> * **ONC-RPC (Sun RPC)** → Remote procedure calls
> * **XDR (External Data Representation)** → Platform-independent data format
> * **UDP/TCP** → Transport layer communication

---

# 🧠 NFS Versions

| Version     | Features                                                         |
| ----------- | ---------------------------------------------------------------- |
| **NFSv2**   | Old, UDP-based, limited functionality                            |
| **NFSv3**   | Better performance, larger file support, improved error handling |
| **NFSv4**   | Stateful, secure, uses Kerberos, single port (2049), ACL support |
| **NFSv4.1** | Adds pNFS (parallel file access), clustering, better scalability |

---

# 🧠 Key Improvements in NFSv4

> [!important]
>
> * Uses **only port 2049**
> * Works better with **firewalls**
> * Supports **Kerberos authentication**
> * Introduces **stateful operations**
> * Adds **ACL (Access Control Lists)**

---

# 🧠 Access Control Model

NFS does NOT use built-in authentication.

Instead, it relies on:

* UID (User ID)
* GID (Group ID)
* UNIX permission model

> ⚠️ Risk: UID mismatch between client and server can lead to privilege issues.

---

# 🧠 Default Configuration

## Config File

```bash
/etc/exports
```

---

## Example Exports File

```bash
/mnt/nfs  10.129.14.0/24(rw,sync,no_subtree_check)
```

---

# 🧠 Export Syntax

```
/directory  client(options)
```

Example:

```
/srv/nfs 192.168.1.0/24(ro,sync)
```

---

# 🧠 NFS Export Options

| Option               | Description                                    |
| -------------------- | ---------------------------------------------- |
| **rw**               | Read & write access                            |
| **ro**               | Read-only access                               |
| **sync**             | Synchronous writes (safer)                     |
| **async**            | Faster but less safe                           |
| **secure**           | Only allow privileged ports (<1024)            |
| **insecure**         | Allow non-privileged ports (>1024)             |
| **no_subtree_check** | Disables subtree verification                  |
| **root_squash**      | Maps root to anonymous user (security feature) |

---

# ⚠️ Dangerous NFS Settings

| Setting              | Risk                         |
| -------------------- | ---------------------------- |
| **rw**               | File modification allowed    |
| **insecure**         | Weak port restrictions       |
| **no_root_squash**   | Full root access from client |
| **no_subtree_check** | Weak directory validation    |

> ⚠️ `no_root_squash` is the most dangerous misconfiguration.

---

# 🧠 Footprinting NFS

## Basic Scan

```bash
sudo nmap -sV -sC -p111,2049 <IP>
```

---

## NFS Script Scan

```bash
sudo nmap --script nfs* -p111,2049 <IP>
```

---

# 🧠 RPC Enumeration Output

> [!info]
> Reveals:
>
> * NFS version
> * Mounted shares
> * Mount services
> * Lock services

---

# 🧠 Show Available Shares

```bash
showmount -e <IP>
```

Example:

```
/mnt/nfs 10.129.14.0/24
```

---

# 🧠 Mount NFS Share

```bash
mkdir target-nfs
sudo mount -t nfs <IP>:/ ./target-nfs -o nolock
cd target-nfs
```

---

# 🧠 Inspect Mounted Share

```bash
ls -l
```

or

```bash
ls -n
```

---

# 🧠 Permission Interpretation

| Type    | Meaning                |
| ------- | ---------------------- |
| `ls -l` | Shows usernames/groups |
| `ls -n` | Shows UID/GID          |

---

# 🧠 NFS File Example Output

```
-rw-r--r-- 1 root root 1872 id_rsa
-rw-r--r-- 1 1000 1000  348 file.pub
```

---

# 🧠 NFS Enumeration Tools

## rpcinfo

```bash
rpcinfo -p <IP>
```

---

## showmount

```bash
showmount -e <IP>
```

---

## Nmap NSE

```bash
nmap --script nfs-ls,nfs-showmount,nfs-statfs <IP>
```

---

# 🧠 Unmount NFS Share

```bash
cd ..
sudo umount ./target-nfs
```

---

# 🧠 NFS Security Risks

| Risk                  | Impact               |
| --------------------- | -------------------- |
| Weak UID mapping      | Privilege escalation |
| Writable exports      | File injection       |
| no_root_squash        | Root compromise      |
| Insecure ports        | Bypass restrictions  |
| Misconfigured exports | Data exposure        |

---

# 🧠 Attack Surface Scenarios

> [!important]
>
> * Extract SSH keys from shared directories
> * Upload malicious scripts if writable
> * Exploit UID mismatch for privilege escalation
> * Pivot using mounted filesystem access

---

# 🧠 Key Enumeration Commands

## Nmap

```bash
nmap -sV -p111,2049 <IP>
```

---

## Show Exports

```bash
showmount -e <IP>
```

---

## Mount Share

```bash
mount -t nfs <IP>:/ ./mnt -o nolock
```

---

## RPC Info

```bash
rpcinfo -p <IP>
```

---

# 🧠 Key Takeaways

> [!summary]
>
> * NFS is widely used in Linux/Unix environments for file sharing.
> * It relies on UID/GID instead of strong authentication (by default).
> * Misconfigurations like `no_root_squash` can lead to full system compromise.
> * Enumeration is critical: always check exports, permissions, and mounted data.
> * Combine tools like `nmap`, `showmount`, and `rpcinfo` for full visibility.
