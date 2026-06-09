

> [!info] 🧠 What is a Password Policy?
> 
> Bash
> 
> ```
> A framework of rules ensuring users create strong passwords and manage them securely
> ```
> 
> 💡 Scope extends across the complete password lifecycle:
> 
> - Creation & Requirements (Length, complexity)
>     
> - Safe Storage & Transmission
>     
> - Lifetime Management (Expiration & rotation rules)
>     

> [!tip] 🔧 Core Components
> 
> Bash
> 
> ```
> Definition (Rules & Standards) ↔ Enforcement (Technical Systems)
> ```
> 
> 💡 Key Mechanisms:
> 
> - **Definition:** Outlines security expectations and baseline criteria
>     
> - **Enforcement:** Technology (e.g., Active Directory GPOs) used to compel compliance
>     
> 
> ✔ Together, they prevent chaotic, unconstrained user behavior

# 🌳 Standards & Expiration Shift

> [!info] 📚 Compliance Baselines
> 
> Bash
> 
> ```
> Established industry standards used to set a secure baseline
> ```
> 
> 💡 Common Frameworks:
> 
> - **NIST SP800-63B** (Digital Identity Guidelines)
>     
> - **CIS Password Policy Guide**
>     
> - **PCI DSS**
>     
> 
> ⚠️ Note: Adhering to standards ensures a baseline, but compliance alone $\neq$ complete security.

> [!tip] 📍 The Expiration Shift
> 
> Bash
> 
> ```
> Modern industry standards recommend disabling forced password expiration
> ```
> 
> 💡 The Logic:
> 
> - **Old Way:** Forced changes every 60–90 days.
>     
> - **The Flaw:** Users resort to predictable mutations (e.g., changing `Inlanefreight01!` to `Inlanefreight02!`).
>     
> - **Modern Standard:** Do not expire passwords unless a compromise is actively confirmed.
>     

# 🔐 Sample Policy & Weaknesses

|**Requirement**|**Traditional Rule**|**Vulnerability / Flaw**|**Better Practice**|
|---|---|---|---|
|**Length**|Min. 8 characters|Too short for brute-force resistance|Increase min. length requirements|
|**Complexity**|Upper, lower, number, symbol|Users still pick guessable terms|Implement strict string blacklists|
|**Rotation**|Change every 60 days|Predictable increment patterns|Shift to context-driven rotation|

# 🚫 Policy Mitigations: Blacklisting

> [!danger] 🔓 Custom Predictable Mutations
> 
> Bash
> 
> ```
> User Choice: Inlanefreight01! (Meets complexity, but highly vulnerable)
> ```
> 
> 💡 Target Phrases to Restrict:
> 
> - **Company Context:** The organization name and highly related terms
>     
> - **Calendar Terms:** Names of months, seasons, or current years
>     
> - **Defaults:** Variations of `password`, `welcome`, `123456`, or `abcde`
>     

# 🔍 Password Creation Strategies

> [!success] 🔑 Random Complexity
> 
> Bash
> 
> ```
> Example: CjDC2x[U
> ```
> 
> 💡 Profile:
> 
> - High entropy; strong defense against brute-forcing and password spraying.
>     
> - High memory friction; practically requires a **Password Manager** (e.g., 1Password) to handle.
>     

> [!success] 📝 Complex Passphrases
> 
> Bash
> 
> ```
> Example: ()The name of my dog is Popy!
> ```
> 
> 💡 Profile:
> 
> - Uses ordinary words or song lyrics combined with special characters.
>     
> - High entropy and easy to remember.
>     
> - ⚠️ Risk: Vulnerable if personal elements are exposed via **OSINT** (Open Source Intelligence).
>     

# 🧩 Mental Model

> [!quote] 🎯 Implementation Hierarchy
> 
> Bash
> 
> ```
> Policy Definition → The Law
> GPO / Tech Controls → The Police Officer
> Password Manager → The Vault
> ```
> 
> 💡 "The Law" (Policy) dictates behavior, "The Police" (GPO) prevents entry if you break it, and "The Vault" (Password Manager) manages the complexity for you.