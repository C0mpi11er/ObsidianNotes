🐍 ODAT (Oracle Database Attacking Tool)

[!info] 🧠 What is ODAT?

Oracle Database Attacking Tool = Oracle enumeration + exploitation framework

💡 Used to:

enumerate Oracle services (TNS)
brute-force credentials
exploit misconfigurations (file upload, RCE, etc.)

✔ Think of it as “Oracle pentesting Swiss army knife”

[!tip] 🔧 Core Idea

Target (1521) → TNS Listener → SID → Credentials → Exploitation

💡 You can:

discover Oracle services
guess database SID
brute-force users/passwords
exploit database features

✔ Everything starts from TNS listener

📡 ODAT Setup (if needed)

[!info] 🧰 Install ODAT

git clone https://github.com/quentinhardy/odat.git
cd odat
pip install -r requirements.txt

💡 Requires:

Python3
Oracle client libraries

✔ Used in HTB / Pwnbox environments

🔍 Basic Enumeration

[!info] 📡 Check Target (All Modules)

./odat.py all -s <target>

💡 Runs:

SID discovery
credential guessing
service checks

✔ First command after confirming port 1521

[!tip] 🧠 Check TNS Listener

./odat.py tnscmd -s <target>

💡 Used to:

verify Oracle listener is alive
gather basic service info

✔ Like banner grabbing

🧩 SID Enumeration

[!info] 🔍 Guess Database SID

./odat.py sidguesser -s <target>

💡 Finds:

database instance names (XE, ORCL, etc.)

✔ Required for authentication

[!tip] 🔎 Guess Service Name

./odat.py snguesser -s <target>

💡 Finds:

service names used by Oracle listener

✔ Alternative to SID in modern Oracle

🔐 Credential Attacks

[!success] 🔑 Password Guessing

./odat.py passwordguesser -s <target> -d <SID>

💡 Uses:

default passwords
weak credentials

✔ Works after SID discovery

[!tip] 📂 Custom Wordlists Attack

./odat.py passwordguesser -s <target> -d <SID> -U users.txt -P passwords.txt

💡 Used for:

brute-force database users

✔ Similar to Hydra but Oracle-specific

⚙️ Exploitation Modules

[!info] 📂 File Upload (UTL_FILE)

./odat.py utlfile -s <target> -d <SID> -U <user> -P <pass> --putFile <remote_path> <local_file>

💡 Used to:

write files to server

✔ Can lead to webshell if web directory is known

[!tip] 🌐 HTTP Request from DB

./odat.py utlhttp -s <target> -d <SID> -U <user> -P <pass>

💡 Used for:

SSRF-style attacks
outbound HTTP requests from DB

[!success] 📡 TCP Connections

./odat.py utltcp -s <target> -d <SID> -U <user> -P <pass>

💡 Used for:

internal network probing from DB

[!danger] 🔥 Privilege Escalation

./odat.py privesc -s <target> -d <SID> -U <user> -P <pass>

💡 Attempts:

escalate DB privileges
gain SYSDBA-like access
🧠 ODAT Attack Mindset

[!quote] 🎯 Think Like This

1. Find listener (1521)
2. Find SID
3. Guess credentials
4. Exploit database features

💡 Oracle attacks are chain-based exploitation

[!warning] ⚠️ Common Mistake

Running passwordguesser without SID

❌ Won’t work
✔ Always: SID → credentials → exploitation

⚡ Real-World Workflow

[!success] 🧠 ODAT Flow

1. nmap -p1521
2. sidguesser
3. snguesser
4. passwordguesser
5. exploitation modules

💡 Full Oracle compromise path

🧩 Mental Model

[!quote] 🎯 Think Like This

Listener → SID → Credentials → Exploitation → Database takeover

💡 ODAT = automation of Oracle attack chain