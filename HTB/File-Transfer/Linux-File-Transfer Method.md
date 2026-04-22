

>[!INFO] **Core Philosophy: The Swiss Army Knife**

Linux is extremely versatile for file transfers because "everything is a file" and **pipes** (`|`) allow you to chain commands together. Most malware and administrators use **HTTP/S** for movement because it is usually allowed through firewalls.

---

>[!TIP] **Key Transfer Methods**

- **Living off the Land (LotL):** Using built-in tools like `wget`, `curl`, and `python` to download payloads.
    
- **Fileless Execution:** Piping a download directly into a shell (e.g., `curl <URL> | bash`) to execute in memory without touching the disk.
    
- **No-Tool Fallback:** Using Bash's internal `/dev/tcp` device to download files when no other utilities are installed.
    
- **Secure Movement:** Leveraging `SCP` (Secure Copy) over SSH for encrypted transfers.
    
- **Exfiltration:** Using `uploadserver` (Python) to send "loot" (like `/etc/shadow`) from the target back to the attacker.
    

---

>[!IMPORTANT] **Security & Integrity**

- **Verification:** Always use `md5sum` or `sha256sum` to ensure a file wasn't corrupted or tampered with during transfer.
    
- **Stealth:** Attackers use Base64 encoding to move files as "text" to bypass deep packet inspection or when a direct network connection isn't available.
    

---

>[!CHECK] **The "Big Three" Summary**

1. **If you have internet:** Use `wget` or `curl`.
    
2. **If you have SSH access:** Use `scp`.
    
3. **If you have nothing:** Use `Base64` copy-paste or `Bash /dev/tcp`.
    
