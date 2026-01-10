Below is a **short, clean Obsidian-style note** summarizing the lecture.

---

# DNS Exfiltration — Quick Summary

## Overview

DNS exfiltration requires controlling a domain and its DNS records. In this lab, THM provides **tunnel.com** and a DNS management web interface.

Use these portals (no VPN needed):

- [http://10.66.168.194](http://10.66.168.194)
    
- [https://10-66-168-194.reverse-proxy.cell-prod-us-east-1c.vm.tryhackme.com](https://10-66-168-194.reverse-proxy.cell-prod-us-east-1c.vm.tryhackme.com)
    

---

## DNS Records Needed

To exfiltrate data, set up a custom nameserver:

1. **A Record**
    
    - Points to the AttackBox IP.
        
    - Example:
        
        ```
        t1ns.tunnel.com → AttackBox_IP
        ```
        
2. **NS Record**
    
    - Delegates a subdomain to your custom nameserver.
        
    - Example:
        
        ```
        t1.tunnel.com → NS → t1ns.tunnel.com
        ```
        

This enables DNS queries such as `data.t1.tunnel.com` to be sent to your controlled server.

---

## Preconfigured THM Nameserver (Optional)

THM provides a ready-to-use DNS server:

|Record|Type|Value|
|---|---|---|
|attNS.tunnel.com|A|172.20.0.200|
|att.tunnel.com|NS|attNS.tunnel.com|

Use `*.att.tunnel.com` for quick exfiltration tests.

---

## DNS Settings on AttackBox

If using the AttackBox for DNS tunneling, set DNS resolver to:

```
nameservers:
  addresses: [10.66.168.194]
  search: [tunnel.com]
```

Apply:

```
sudo netplan apply
```

---

## Testing

Verify DNS is functioning on JumpBox:

```
dig +short test.thm.com
dig +short test.tunnel.com
```

Expected:

```
127.0.0.1
```

Ping test:

```
ping test.thm.com -c 1
```

---

## Final Task

Resolve the flag:

```
dig +short flag.thm.com
```

---

