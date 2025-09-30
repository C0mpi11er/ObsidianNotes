vulnerability assessment -> is simply scanning as many host on the network as possible but not exploiting any

Pen testing ->  is vulnerability assessment but added work of further trying to exploit those vulnerability 


APT -> mean advanced persistent threat this threat actors are usually government sponsored hacker and their seen as persistent because their methods of exploitation are usually seen as undetected fro a long time or not at all


Red Teaming -> is basically pen testing but focused more on detection and response 


read team objectives are refereed to as flags or crowns jewels 

TTP's -> mean tactics techniques and procedures


Engagement structure -> core function of this is to emulate real world threat actor simulation and theri various cyber kill chain methotholigies
e.g 
Lockheed martin cyber kill chain 
![[THE-CYBER-KILL-CHAIN-body.png.pc-adaptive.1280.medium.png]]


   Unified Kill Chain
    Varonis Cyber Kill Chain
    Active Directory Attack Cycle
    MITRE ATT&CK Framework etc ``



|Technique|Purpose|Examples|
|---|---|---|
|Reconnaissance|Obtain information on the target|Harvesting emails, OSINT|
|Weaponization|Combine the objective with an exploit. Commonly results in a deliverable payload.|Exploit with backdoor, malicious office document|
|Delivery|How will the weaponized function be delivered to the target|Email, web, USB|
|Exploitation|Exploit the target's system to execute code|MS17-010, Zero-Logon, etc.|
|Installation|Install malware or other tooling|Mimikatz, Rubeus, etc.|
|Command & Control|Control the compromised asset from a remote central controller|Empire, Cobalt Strike, etc.|
|Actions on Objectives|Any end objectives: ransomware, data exfiltration, etc.|Conti, LockBit2.0, etc.|
   