>[!info] 🔐 Hashing
Passwords are hashed before storage to protect them if leaked.
Hashing is a one-way function (cannot be reversed easily).
Examples: MD5, SHA-256

>[!danger] ⚠️ Password Cracking
Attackers try to recover the original password from a hash using different techniques.

>[!tip] 🌈 Rainbow Tables
Precomputed tables of password → hash mappings

Very fast lookup
Only works if no salt is used
Completely broken by salting

>[!important] 🧂 Salting
A random value added to a password before hashing

salt + password → hash
Each password should have a unique salt
Salt is not secret (stored with hash)
Prevents rainbow table attacks

>[!success] ✔ Why it works
Makes precomputed attacks impractical by massively increasing combinations.

>[!warning] 💥 Brute Force Attack
Tries every possible combination of characters

✅ Guaranteed to succeed eventually
❌ Very slow for long/complex passwords

>[!info] ⚡ Speed Insight
Depends on:

Hardware (GPU vs CPU)
Hash type (MD5 is fast, stronger hashes slower)

>[!example] 📖 Dictionary Attack
Uses a wordlist of common passwords instead of all combinations

Faster than brute force
Very effective in real-world scenarios
Example: rockyou.txt

>[!summary] 🧠 Key Takeaways

Hashing protects stored passwords
Salting protects against precomputed attacks
Dictionary attacks are most practical
Brute force is slow but guaranteed