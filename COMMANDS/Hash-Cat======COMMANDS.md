1
>[!info] ⚡ Hashcat (v7.1.2) Overview
Hashcat = GPU-accelerated password cracker

Supports hundreds of hash types
Extremely fast (GPU-based)
Uses attack modes + hash modes

>[!important] 🚀 Basic Usage

hashcat [options] hash|hashfile [wordlist|mask|dir]

Core flags:

-m → hash type
-a → attack mode

>[!example]

hashcat -a 0 -m 0 hashes.txt rockyou.txt

>[!tip] 🔑 Core Options (MUST KNOW)

-m, --hash-type → define hash type
-a, --attack-mode → define attack
-h, -hh → help (all hash modes with -hh)
-V → version
--quiet → silent mode

>[!important] 🔢 Attack Modes

0 → Straight (dictionary)
1 → Combination
3 → Brute-force (mask)
6 → Hybrid (wordlist + mask)
7 → Hybrid (mask + wordlist)
9 → Association

>[!example]

hashcat -a 3 -m 0 hash.txt ?a?a?a?a

>[!important] 🔍 Hash Modes

Defined using -m
View all:
hashcat -hh

>[!example] Common

0 → MD5
100 → SHA1
1400 → SHA-256
1700 → SHA-512

>[!warning] ⚠️ Critical
Wrong -m = no cracking happens

>[!tip] 📖 Dictionary Attack (Most Used)

hashcat -a 0 -m </id> hash.txt wordlist.txt

>[!success] ✔ Best first attempt

>[!tip] 🔄 Rules (VERY IMPORTANT)

hashcat -a 0 -m 0 hash.txt rockyou.txt -r rules/best64.rule

>[!info]

Modifies words:
pass → Pass123!
Rules location:
/usr/share/hashcat/rules/

>[!warning] 💥 Mask Attack (Brute-force Smart)

hashcat -a 3 -m 0 hash.txt '?u?l?l?l?l?d?s'

>[!info] Built-in charsets

?l → lowercase
?u → uppercase
?d → digits
?s → symbols
?a → all
?b → full byte

>[!tip] 🎯 Custom Charset

-1 abc123
hashcat -a 3 -m 0 hash.txt '?1?1?1'

>[!important] 📊 Output & Results

--show → show cracked hashes
--left → show uncracked
-o file.txt → save output

>[!example]

hashcat --show hash.txt

>[!tip] 🧠 Session Management

--session=myjob
--restore
--runtime=3600

✔ Resume interrupted jobs

[!info] 📁 Potfile (Stored Cracks)

Default storage of cracked passwords
--potfile-path=my.pot
--potfile-disable

[!tip] ⚡ Performance & Hardware

-d → select device
-D → device type (CPU/GPU)
-w → workload profile (1–4)

>[!example]

hashcat -d 1 -w 3 hash.txt

>[!warning] 🌡️ Hardware Safety

--hwmon-temp-abort=100

✔ Prevent overheating

>[!tip] 📊 Benchmarking

hashcat -b
hashcat -b --benchmark-all

✔ Test speed before attack

>[!important] 🔄 Rule Debugging

--debug-mode=4
--debug-file=debug.log

✔ See how rules transform words

>[!tip] 🔁 Candidate Control

--stdout          # print candidates only
--keyspace        # show total combinations
--skip=N          # skip candidates
--limit=N         # limit candidates

>[!info] 🔍 Hash Identification

hashcat --identify hash.txt

OR use hashid -m

>[!tip] 🧬 Increment Mode (Mask Expansion)

-i --increment
--increment-min=4
--increment-max=8

✔ Tries increasing lengths

[!warning] 🧠 Markov (Smart Guessing)

--markov-disable
--markov-classic
--markov-inverse
-t 50

✔ Probability-based brute-force

>[!tip] 🌐 Brain Feature (Distributed Cracking)

--brain-server
--brain-client
--brain-host=127.0.0.1

✔ Avoid duplicate work across systems

>[!summary] 🧠 Real-World Workflow

Identify hash:
hashcat --identify hash.txt
Dictionary:
hashcat -a 0 -m </id> hash.txt rockyou.txt
Add rules:
hashcat -a 0 -m </id> hash.txt rockyou.txt -r best64.rule
Mask (if pattern known):
hashcat -a 3 -m </id> hash.txt '?pattern'
Check results:
hashcat --show hash.txt

>[!success] ✔ Key Takeaways

-a = attack mode, -m = hash type
Dictionary + rules = most effective combo
Mask = efficient brute-force
GPU = massive speed advantage
Always verify hash type before attacking