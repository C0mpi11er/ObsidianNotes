This is a high-structure, **HTB Academy-style** conversion of your Nibbles documentation. It is designed to be copy-pasted directly into your Obsidian vault, maintaining professional formatting and clear Attacker/Victim role distinction.

---

# 📑 HTB: Nibbles - Documentation

### 🎯 Target Information

|**Detail**|**Value**|
|---|---|
|**Target OS**|Linux|
|**Difficulty**|Easy|
|**IP Address**|`<TARGET_IP>`|
|**Primary Vectors**|Web Exploitation / Sudoers Misconfiguration|

### 🔗 External Resources

- **Video:** [Ippsec Nibbles Walkthrough](https://www.google.com/search?q=https://www.youtube.com/watch%3Fv%3DD_9vP_UIn3E)
    
- **Write-up:** [0xdf Nibbles Analysis](https://0xdf.gitlab.io/2018/06/30/htb-nibbles.html)
    

---

## 🏁 1. Penetration Testing Methodologies

|**Type**|**Knowledge Level**|**Focus**|
|---|---|---|
|**Black-Box**|Zero Knowledge|Deep reconnaissance; simulates external threats.|
|**Grey-Box**|Partial (IPs, Credentials)|Internal threat simulation; focuses on misconfigurations.|
|**White-Box**|Full (Source, Architecture)|Comprehensive audit; logic flaw hunting.|

---

## 🔍 2. Initial Enumeration `[A]`

### Nmap Service Discovery

|**Command**|**Description**|
|---|---|
|`nmap -sV --open -oA nibbles_initial_scan <TARGET_IP>`|**Initial Scan:** Identify standard open ports and service versions.|
|`nmap -p- --open -oA nibbles_full_tcp_scan <TARGET_IP>`|**Full Scan:** Checks all 65,535 ports for non-standard services.|
|`nmap -sC -sV -p 22,80 -oA nibbles_script_scan <TARGET_IP>`|**Script Scan:** Runs default NSE scripts against identified ports.|
|`nmap -sV --script=http-enum -oA nibbles_http_enum <TARGET_IP>`|**HTTP Enum:** Automated discovery of web directories and files.|

### Manual Service Verification

|**Command**|**Description**|
|---|---|
|`nc -nv <TARGET_IP> 22`|**Banner Grabbing:** Manually verify the SSH service version.|
|`curl -I http://<TARGET_IP>`|**HTTP Headers:** Check web server headers for version info or custom fields.|

---

## 📊 3. Discovery Results Summary

### Service Footprint

|**Port**|**Service**|**Version**|
|---|---|---|
|**22/tcp**|SSH|OpenSSH 7.2p2 (Ubuntu)|
|**80/tcp**|HTTP|Apache httpd 2.4.18|

### Key Observations

- **Attack Surface:** Extremely limited; only SSH and Web are accessible.
    
- **Approach:** Utilizing a **Grey-Box** methodology as the target scope is predefined.
    
- **Hygiene:** All scans are documented and saved using the `-oA` flag for full output logging.
    

---

## 📝 4. Progression Notes

> [!TIP]
> 
> **Next Steps:**
> 
> 1. Navigate to `http://<TARGET_IP>/` and view the page source for hidden directories or comments.
>     
> 2. Fuzz for common CMS paths (e.g., `/nibbleblog/`, `/admin/`).
>     
> 3. Test for default credentials if a login portal is discovered.
>     



>[!TIP]
```
 xsltproc scan.xml -o scan.html
```
to get browser clean version 

---

#Enumeration #Nmap #Nibbles #Linux #HTB-Academy