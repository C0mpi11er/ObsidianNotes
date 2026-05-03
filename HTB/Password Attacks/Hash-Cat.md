>[!info] ⚡ Hashcat Overview
Hashcat is a high-performance password cracking tool (Linux, Windows, macOS).

Initially proprietary (2009–2015), now open-source
Strong GPU acceleration → extremely fast
Supports hundreds of hash types & attack modes
Similar purpose to John the Ripper

>[!important] 🚀 Basic Syntax

hashcat -a <attack_mode> -m <hash_type> </hashes> [wordlist/mask/rules]
-a → attack mode
-m → hash type ID
</hashes> → hash string or file
Extra args depend on attack

>[!tip] 🔢 Hash Types (CRITICAL)

Each hash has a numeric ID
View list:
hashcat --help

>[!example] Common IDs

0 → MD5
100 → SHA1
1400 → SHA-256
1700 → SHA-512
6000 → RIPEMD-160

>[!warning] ⚠️ Important
Wrong -m = attack will fail

>[!tip] 🔍 Identifying Hash Type
Use:

hashid -m </hash>

✔ Suggests Hashcat mode ID

>[!warning] Reality

Multiple matches possible
Use context (source of hash)

>[!important] 📖 Dictionary Attack (-a 0)
Most common attack

>[!example] Basic

hashcat -a 0 -m 0 hash.txt rockyou.txt

>[!info] How it works

Tests each word in wordlist
Stops when match is found

✔ Fast and practical

>[!tip] 🔄 Dictionary + Rules (VERY POWERFUL)

hashcat -a 0 -m 0 hash.txt rockyou.txt -r best64.rule

>[!info] Rules do:

Add numbers → pass → pass123
Leetspeak → a → @
Capitalization

>[!example] Rule location

/usr/share/hashcat/rules/

✔ Multiplies effectiveness of wordlists

>[!warning] 💥 Mask Attack (-a 3)
Smart brute-force with known structure

>[!example]

hashcat -a 3 -m 0 hash.txt '?u?l?l?l?l?d?s'

>[!info] Example pattern

1 uppercase
4 lowercase
1 digit
1 symbol

✔ Much faster than full brute-force

>[!tip] 🎯 Built-in Charset Symbols

?l → lowercase
?u → uppercase
?d → digits
?s → symbols
?a → all (l+u+d+s)
?h → hex lowercase
?H → hex uppercase
?b → all bytes

>[!important] 🔧 Custom Charset

-1 abcdef123
hashcat -a 3 -m 0 hash.txt '?1?1?1'
Define custom sets with -1, -2, -3, -4

>[!info] ⚡ Performance Insight

GPU-based → much faster than CPU tools
Example speeds:
kH/s → thousands/sec
MH/s → millions/sec

✔ Always prefer GPU if available

>[!example] 📊 Output Key Fields

Status: Cracked → success
Speed → cracking speed
Recovered → hashes cracked
Progress → how far done
Candidates → current guesses

>[!warning] ⚠️ Common Mistakes

Wrong -m (hash type)
Weak wordlist
Not using rules
Ignoring mask when pattern is known

>[!summary] 🧠 Attack Strategy (REAL WORLD)

Identify hash:
hashid -m </hash>
Try dictionary:
hashcat -a 0 -m </id> hash.txt rockyou.txt
Add rules:
hashcat -a 0 -m </id> hash.txt rockyou.txt -r best64.rule
Use mask (if pattern known):
hashcat -a 3 -m </id> hash.txt '?pattern'

>[!success] ✔ Key Takeaways

Hashcat = fastest practical cracking tool (GPU)
-a = attack mode, -m = hash type
Dictionary + rules = most effective combo
Mask attack = efficient brute-force
Correct hash identification is critical