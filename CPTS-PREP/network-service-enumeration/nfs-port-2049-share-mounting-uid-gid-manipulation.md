```markdown
# 🛰️ NFS - Port 2049, share mounting, UID/GID manipulation

---

## 🔍 Overview and Initial Exploration

> [!ABSTRACT]
> Network File System (NFS) is a distributed file system protocol allowing Unix-like systems to access files over a network as if they were local. Port 2049 is the default port used for NFS services.

---

### Enumeration with Nmap

```bash
sudo nmap -p 2049 <IP>
```

> [!INFO]
> This command scans for the NFS service running on port 2049 of a specific IP address.

---

## 🔧 Mounting Shares and UID/GID Manipulation

### Mount an NFS Share

Mounting an NFS share requires specifying options such as `vers`, `proto`, and `rsize/wsize`.

```bash
sudo mount -t nfs <IP>:/export /mnt -o vers=3,proto=tcp,rsize=8192,wsize=8192,hard,timeo=600,retrans=2
```

> [!NOTE]
> Replace `vers`, `proto`, and paths with appropriate values based on the environment.

### Check UID/GID Mapping

When mounting an NFS share, you may need to specify UID/GID mapping if different from local user IDs. Use the `nfs` client options `all_squash`, `anonuid`, and `anongid`.

```bash
sudo mount -t nfs <IP>:/export /mnt -o vers=3,proto=tcp,rsize=8192,wsize=8192,hard,timeo=600,retrans=2,all_squash,anonuid=<UID>,anongid=<GID>
```

### Example of UID/GID Mapping

```bash
sudo mount -t nfs 192.168.56.10:/home /mnt -o vers=3,proto=tcp,rsize=8192,wsize=8192,hard,timeo=600,retrans=2,all_squash,anonuid=1000,anongid=1000
```

> [!WARNING]
> Ensure proper permissions and mappings to avoid unauthorized access.

---

## 🛠️ Testing for Vulnerabilities

### Verifying Mounting Permissions

After mounting an NFS share, check the ownership of files within the mounted directory.

```bash
ls -l /mnt/
```

> [!CHECK]
> Confirm if the files have correct permissions and are accessible according to UID/GID mappings.

---

## 🔑 Manipulating User Privileges

### Setting Up a Custom UID/GID Mapping

If you want to manipulate privileges, create custom UID/GID mappings in `/etc/idmapd.conf` or use command-line options during mounting.

```bash
sudo nano /etc/idmapd.conf
```

Add the following under `[Static]`:

```text
[Static]
<IP>.home = 1000:1000
```

> [!SUCCESS]
> This will map the NFS share home directory to local UID/GID 1000.

---

## 📑 Important Considerations

### Security Risks

- **Unauthorized Access**: Incorrect mapping or default settings can lead to unauthorized access.
- **Privilege Escalation**: Manipulating UIDs and GIDs improperly can result in privilege escalation.

> [!DANGER]
> Be cautious with UID/GID manipulations, especially when dealing with system files and directories.

---

## 🔍 Further Investigation

### NFS Vulnerability Assessment

Use tools like `nmap` scripts or custom scripts to assess the security of NFS shares.

```bash
sudo nmap --script=nfs-* -p 2049 <IP>
```

> [!QUESTION]
> Explore additional methods and scripts for thorough testing and validation.

---

## 🧠 Mental Model

### NFS Exploitation Process

1. **Identify NFS Shares**
   ```text
   sudo nmap -sV --script=nfs* -p 2049 <IP>
   ```
2. **Mount the Share**
   ```bash
   sudo mount -t nfs <IP>:/export /mnt ...
   ```
3. **Verify Permissions and Mapping**
   ```bash
   ls -l /mnt/
   ```
4. **Manipulate UIDs/GIDs for Privilege Escalation**

> [!SUCCESS]
> Follow these steps to systematically test NFS shares for security vulnerabilities.

---

## 🔍 Additional Resources

- [[Nmap]]
- [[CrackMapExec]]
- [[Metasploit]]

```