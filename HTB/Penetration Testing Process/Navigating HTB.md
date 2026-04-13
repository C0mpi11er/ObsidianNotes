# 🎮 Hack The Box Ecosystem Overview

Understanding the HTB layout is essential for navigating your way from a "Noob" to "Omniscient."

## 📊 Core Stats & Standing

- **Profile:** Your central dashboard for rank, owned machines/challenges, and earned badges.
    
- **Rankings:** Shows where you stand globally, by country, or within your team.
    
    - ==Active Machines/Challenges== contribute to your public rank.
        
    - ==Retired content== contributes to your VIP rank.
        

---

## 🛤️ Learning & Practice

> [!abstract] **Tracks** Curated collections of machines and challenges focused on a specific theme (e.g., **Active Directory**, **Pivoting**, or **OSCP Prep**). Enrolling in a track provides a guided roadmap rather than jumping into random machines.

> [!example] **Challenges** Small, bite-sized tasks categorized by discipline (Web, Crypto, Pwn, Reversing, etc.). Great for focused skill-building when you don't have time for a full machine.

---

## 🖥️ The Machines

The heart of HTB. To play, you must connect via the **HTB VPN**.

|Type|Points?|Walkthroughs?|Access|
|---|---|---|---|
|**Active**|==Yes==|No (Strictly prohibited)|Free (20 machines)|
|**Retired**|No|==Yes== (Video/Text)|VIP Subscription|

Export to Sheets

---

## 🏰 Specialized & Advanced Labs

As you level up, you unlock complex environments that simulate real-world networks.

- **Fortress:** Labs built by external companies. Solving these can sometimes lead to **job offers**.
    
    - _Requirement:_ Rank **Hacker** or above.
        
- **Endgame:** Multi-machine networks with a single attack path. Designed to mimic a professional engagement.
    
    - _Requirement:_ Rank **Guru** or above (for Active ones).
        
- **Pro Labs:** The "Final Boss" of HTB. Large-scale enterprise infrastructures (like _Dante_ or _APT Labs_) that require a separate subscription. Completion earns a **Certificate**.
    

---

## ⚔️ Competitive Hacking (Battlegrounds)

Real-time, multiplayer hacking matches.

> [!quote] **Modes**
> 
> - **Cyber Mayhem:** Attack/Defense (Defend your boxes while rooting the enemy's).
>     
> - **Server Siege:** Pure speed (The fastest team to compromise the target wins).
>     

---

### 💡 Quick Pro-Tip for your Notes

If you are pushing for certifications, focus on the **Active Machines** to keep your ranking high and the **Retired Machines** (with walkthroughs) to learn the specific "tricks" of the trade without getting stuck for days.

Would you like a breakdown of which **Pro Labs** specifically focus on the tech stack you're currently mastering?



1. **Consume:** Read the theory in the Academy.
    
2. **Mimic:** Perform the specific task in the guided module lab.
    
3. **Synthesize:** Go to a "Retired Machine" on the main platform that uses that tech.
    
4. **Struggle:** Try to root it without a walkthrough.
    
5. **Validate:** Check an **IppSec** video or a write-up _only_ when you are truly stuck to see the "pro way" of doing what you just tried.
    

---

> [!tip] **Pro-Tip for Obsidian** When you do these labs, don't just copy-paste the commands. Write down **why** a command failed. Your "Failed Commands" log is often more valuable for hardening your knowledge than the successful ones, because it teaches you the edge cases and common syntax errors.


While the **CPTS (Certified Penetration Testing Specialist)** path on HTB Academy is comprehensive, practicing on retired standalone machines is the best way to develop the "creative" chaining required for the exam.

Since the CPTS curriculum is divided into core domains, here are the most relevant **retired machines** grouped by the modules they reinforce.

---

## 🛠️ Infrastructure & Enumeration

**Modules:** _Network Enumeration with Nmap, Footprinting, Attacking Common Services_ These machines focus on service misconfigurations rather than complex web exploits.

|Machine|Difficulty|Focus Area|
|---|---|---|
|**Lame**|Easy|Basic service enumeration (Samba/FTP)|
|**Legacy**|Easy|Classic SMB exploitation (MS08-067)|
|**Blue**|Easy|Modern SMB exploitation (EternalBlue)|
|**Nibbles**|Easy|Initial foothold via service misconfiguration|

---

## 🌐 Web Application Attacks

**Modules:** _SQL Injection, File Inclusion, File Upload Attacks, Command Injections_ These target specific web vulnerabilities taught in the "Web Edition" modules.

|Machine|Difficulty|Focus Area|
|---|---|---|
|**Bashed**|Easy|Web shells and command execution|
|**Validation**|Easy|SQL Injection fundamentals|
|**Devel**|Easy|FTP/Web misconfiguration & file uploads|
|**Pandora**|Easy|Chaining SQLi to gain access (CPTS-style)|
|**Bounty**|Medium|File upload bypass and web-to-shell|

---

## 🔑 Password Attacks & PrivEsc

**Modules:** _Password Attacks, Linux/Windows Privilege Escalation_ Focus on moving from a low-privileged user to Root/System.

|Machine|Difficulty|Focus Area|
|---|---|---|
|**Valentine**|Easy|Heartbleed & SSH key extraction|
|**Optimum**|Easy|Windows exploit suggester & kernel exploits|
|**Cronos**|Medium|Linux Cron job abuse|
|**Sau**|Easy|SSRF and service-based privilege escalation|

---

## 🛡️ Active Directory (AD)

**Modules:** _Active Directory Enumeration & Attacks_ AD is a massive part of CPTS. These machines are essential for mastering Kerberoasting and ACL abuse.

|Machine|Difficulty|Focus Area|
|---|---|---|
|**Active**|Easy|GPP (Group Policy Preference) & Kerberoasting|
|**Forest**|Easy|AS-REP Roasting & BloodHound usage|
|**Cascade**|Medium|LDAP enumeration & group-based escalations|
|**Sauna**|Easy|User enumeration & DCSync|

---

## 🚀 Post-Exploitation & Pivoting

**Modules:** _Pivoting, Tunneling, and Port Forwarding_ Pivoting is the "make or break" skill for the CPTS exam environment.

|Machine|Difficulty|Focus Area|
|---|---|---|
|**Wrecker**|Medium|Challenging network pivoting|
|**Intelligence**|Medium|Chaining web, AD, and network internal services|

> [!TIP] **The Secret Sauce:** After completing the entire path, Hack The Box provides an official **CPTS Preparation Track**. This track contains a curated list of retired machines specifically selected to mirror the difficulty and "thinking" style of the CPTS exam.