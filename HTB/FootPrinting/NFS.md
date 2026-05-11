---

# 📁 NFS (Network File System) Cheat Sheet

---
Here is your note structured **exactly in your format (Obsidian / HTB style)**:

---

# 🐧 NFS (Network File System) Cheat Sheet

---

> [!info] 🧠 What is NFS?
>
> ```bash
> Network File System for sharing files over a network
> ```
>
> 💡 Developed by Sun Microsystems
>
> ✔ Allows remote file systems to be accessed like local folders
>
> ✔ Common in Linux/Unix environments

---

> [!tip] 🎯 Why NFS is Used
>
> ```bash
> Centralized file sharing across systems
> ```
>
> 💡 Used for:
>
> * shared storage
> * backups
> * distributed environments
>
> ✔ Think: “network drive for Linux systems”

---

# 🔁 NFS vs SMB

---

> [!info] 🧠 Key Difference
>
> ```bash
> NFS → Linux/Unix systems
> SMB → Windows systems
> ```
>
> 💡 Important:
>
> * NFS cannot directly communicate with SMB
> * Uses different protocols entirely
>
> ✔ Same goal, different ecosystems

---

# 📦 NFS Versions

---

> [!info] 🧠 NFS Evolution
>
> ```bash
> Different versions = different security & features
> ```

| Version | Features                                |
| ------- | --------------------------------------- |
| NFSv2   | Old, UDP-based, basic                   |
| NFSv3   | Better performance, variable file sizes |
| NFSv4   | Secure, firewall-friendly, stateful     |
| NFSv4.1 | Cluster support, parallel access (pNFS) |

---

> [!success] 🔐 NFSv4 Improvements
>
> ```bash
> Single port: 2049 TCP/UDP
> ```
>
> 💡 Benefits:
>
> * firewall-friendly
> * supports Kerberos authentication
> * ACL support
> * better performance

---

# 🔐 Authentication Model

---

> [!warning] ⚠️ NFS Security Model
>
> ```bash
> No built-in authentication in core NFS protocol
> ```
>
> 💡 Instead relies on:
>
> * RPC authentication layer
> * UID / GID mapping

---

> [!danger] 🚨 UID/GID Risk
>
> ```bash
> Identity = numeric UID, not username
> ```
>
> 💡 Problem:
>
> * mismatch between client/server users
> * server trusts client mapping
>
> ✔ Can lead to unauthorized access in misconfigured systems

---

# ⚙️ RPC & NFS Architecture

---

> [!info] 🧠 NFS Dependency
>
> ```bash
> NFS uses ONC-RPC (Sun RPC)
> ```
>
> 💡 Uses:
>
> * Port 111 → rpcbind
> * Port 2049 → NFS service
>
> ✔ Uses XDR for data exchange

---

# 📁 Default Configuration

---

> [!info] 🧠 /etc/exports File
>
> ```bash
> Defines NFS shared directories
> ```
>
> 💡 Example:
>
> ```bash
> /srv/nfs 10.0.0.0/24(rw,sync,no_subtree_check)
> ```

---

> [!tip] 🔧 Common Export Options
>
> ```bash
> rw        → read/write access
> ro        → read-only
> sync      → safe but slower
> async     → faster but less safe
> insecure  → allows high ports (>1024)
> no_root_squash → root keeps root privileges
> root_squash     → maps root to anonymous user
> ```

---

> [!warning] ⚠️ Dangerous Configurations
>
> ```bash
> no_root_squash
> insecure
> rw on public networks
> ```
>
> 💡 Risks:
>
> * privilege escalation
> * file tampering
> * unauthorized root-level access

---

# 🔍 Footprinting NFS

---

> [!info] 🧠 Key Ports
>
> ```bash
> 111  → rpcbind
> 2049 → NFS service
> ```

---

> [!tip] 🔍 Service Enumeration
>
> ```bash
> nmap -p111,2049 -sV -sC <IP>
> ```

💡 Shows:

* RPC services
* NFS version
* mount points

---

> [!success] 📡 Show Exported Shares
>
> ```bash
> showmount -e <IP>
> ```
>
> 💡 Reveals:
>
> * shared directories
> * allowed client IP ranges

---

# 📂 Mounting NFS Shares

---

> [!info] 🧠 Mount Process
>
> ```bash
> mount remote share locally
> ```

```bash
mkdir nfs_target
sudo mount -t nfs <IP>:/ ./nfs_target -o nolock
```

---

> [!tip] 📁 Explore Share
>
> ```bash
> cd nfs_target
> ls -la
> ```
>
> 💡 You can see:
>
> * files
> * permissions
> * ownership (UID/GID)

---

# 👤 UID / GID Mismatch Concept

---

> [!warning] ⚠️ Key Weakness
>
> ```bash
> NFS trusts numeric UID mapping
> ```
>
> 💡 Issue:
>
> * same UID ≠ same user across systems
>
> ✔ leads to permission confusion

---

> [!success] 🔍 Why This Matters
>
> ```bash
> root on client != root on server (sometimes)
> ```
>
> 💡 Depends on:
>
> * root_squash settings
> * UID mapping consistency

---

# ⚠️ Root Squash Concept

---

> [!danger] 🧠 root_squash
>
> ```bash
> root → anonymous user
> ```
>
> 💡 Purpose:
>
> * prevents root-level remote file control
>
> ✔ Security feature

---

> [!warning] 🚨 no_root_squash Risk
>
> ```bash
> root stays root over NFS
> ```
>
> 💡 Impact:
>
> * full file control
> * privilege escalation risk
>
> ✔ critical misconfiguration

---

# 🔐 Permission View

---

> [!tip] 📊 Check Ownership
>
> ```bash
> ls -l
> ls -n
> ```
>
> 💡 Shows:
>
> * usernames
> * UID/GID mapping

---

# 🔁 Unmounting NFS

---

> [!info] 🧠 Cleanup
>
> ```bash
> sudo umount ./nfs_target
> ```
>
> 💡 Always unmount after analysis

---

# 🧠 Attacker Mindset

---

> [!quote] 🎯 Think Like This
>
> ```bash
> Can I access exported directories?
> Can I manipulate UID/GID mapping?
> Is root_squash disabled?
> Can I read sensitive files?
> ```
>
> 💡 NFS = trust-based file access system

---

# ⚡ Real-World Workflow

---

> [!success] 🧠 NFS Enumeration Flow
>
> ```bash
> 1. Scan ports (111, 2049)
> 2. Identify RPC/NFS service
> 3. Show exported shares (showmount)
> 4. Mount share locally
> 5. Check permissions & UID mapping
> 6. Look for misconfigurations (no_root_squash)
> 7. Extract sensitive files
> ```

---

# 🧩 Mental Model

---

> [!quote] 🎯 Core Idea
>
> ```bash
> NFS = Shared filesystem + weak identity trust
> ```
>
> 💡 Security depends entirely on configuration correctness

---

If you want next, I can also convert this into:

* 🟢 SSH cheat sheet (same format)
* 🟢 SMB exploitation notes
* 🟢 full “Linux Services Enumeration Bible”
* 🟢 ShadowMap module documentation style notes


