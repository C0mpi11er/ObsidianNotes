
---

## 🧠 VulnHub VM Lab List – Beginner to Advanced

### 👶 Beginner Level (Peasant to Apprentice)

- **DC Series (1–10)**  
    _By DCAU_  
    Great for learning the basics: web enumeration, common CVEs, simple privesc.  
    🔗 [https://www.vulnhub.com/series/dc,178/](https://www.vulnhub.com/series/dc,178/)
    
- **Mr. Robot**  
    _Inspired by the show. CTF-style challenges._  
    Web exploits, hashes, basic Linux enumeration.  
    🔗 [https://www.vulnhub.com/entry/mr-robot-1,151/](https://www.vulnhub.com/entry/mr-robot-1,151/)
    
- **Basic Pentesting 1**  
    _By Jose Arteaga_  
    Simple but realistic, covers FTP, SMB, WordPress, privesc.  
    🔗 [https://www.vulnhub.com/entry/basic-pentesting-1,216/](https://www.vulnhub.com/entry/basic-pentesting-1,216/)
    
- **RickdiculouslyEasy 1–3**  
    _By Luke_  
    Humor + solid web & Linux enumeration + scripting.  
    🔗 [https://www.vulnhub.com/entry/rickdiculouslyeasy-1,208/](https://www.vulnhub.com/entry/rickdiculouslyeasy-1,208/)
    
- **Blogger 1**  
    SQLi, admin panel discovery, file upload privesc.  
    🔗 [https://www.vulnhub.com/entry/blogger-1,374/](https://www.vulnhub.com/entry/blogger-1,374/)
    

---

### 🧗‍♂️ Intermediate Level (Knight to Warrior)

- **Hackable II**  
    More complex web enumeration, reverse shells, basic privilege escalation.  
    🔗 [https://www.vulnhub.com/entry/hackable-ii-2,266/](https://www.vulnhub.com/entry/hackable-ii-2,266/)
    
- **Matrix 1–2**  
    Unique puzzles with solid technical depth.  
    🔗 [https://www.vulnhub.com/entry/matrix-1,348/](https://www.vulnhub.com/entry/matrix-1,348/)
    
- **Fristileaks 1.3**  
    Great intro to binary exploitation and service abuse.  
    🔗 [https://www.vulnhub.com/entry/fristileaks-13,133/](https://www.vulnhub.com/entry/fristileaks-13,133/)
    
- **Stapler 1**  
    Multi-vector exploitation, great for building enumeration skills.  
    🔗 [https://www.vulnhub.com/entry/stapler-1,208/](https://www.vulnhub.com/entry/stapler-1,208/)
    
- **LazySysAdmin**  
    Focused on privilege escalation and Windows misconfigs.  
    🔗 [https://www.vulnhub.com/entry/lazysysadmin-1,239/](https://www.vulnhub.com/entry/lazysysadmin-1,239/)
    

---

### 🔥 Advanced Level (Elite to God Tier)

- **SickOS 1.1–1.2**  
    Advanced enumeration, SSH tunneling, kernel exploits.  
    🔗 [https://www.vulnhub.com/entry/sickos-11,132/](https://www.vulnhub.com/entry/sickos-11,132/)
    
- **Kioptrix Series (1–5)**  
    A classic progression series. Learn everything from web to kernel privesc.  
    🔗 [https://www.vulnhub.com/series/kioptrix,6/](https://www.vulnhub.com/series/kioptrix,6/)
    
- **PwnLab: Init**  
    SQLi + LFI + reverse shell + privesc combo.  
    🔗 [https://www.vulnhub.com/entry/pwnlab-init,158/](https://www.vulnhub.com/entry/pwnlab-init,158/)
    
- **HA: Vulnix**  
    NFS misconfig, cronjob exploits, manual enumeration.  
    🔗 [https://www.vulnhub.com/entry/hackademic-rtb1,27/](https://www.vulnhub.com/entry/hackademic-rtb1,27/)
    
- **Zorz 1.0.1**  
    Includes JWT cracking, binary abuse, realistic attack surface.  
    🔗 [https://www.vulnhub.com/entry/zorz-101,754/](https://www.vulnhub.com/entry/zorz-101,754/)
    
- **BSides Vancouver 2018: Workshop**  
    Intended for post-exploitation, privilege escalation, and red teaming.  
    🔗 [https://www.vulnhub.com/entry/bsides-vancouver-2018-workshop,290/](https://www.vulnhub.com/entry/bsides-vancouver-2018-workshop,290/)
    

---

## 📦 Setup Tip for Each VM

Use **VirtualBox** or **VMware** (with NAT or Host-only networking) to run these boxes. Connect your **Kali** VM and scan with:

```bash
netdiscover -r 192.168.X.0/24
nmap -sC -sV -oN initial.txt <TARGET_IP>
```

---

Would you like me to generate **Markdown notes** for each VM as a template to use in Obsidian?