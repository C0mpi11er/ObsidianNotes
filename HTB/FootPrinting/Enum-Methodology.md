
# 🧠 Enumeration Methodology — Cheat Sheet Notes

---

> [!info] 🎯 What is this methodology?
> Enumeration methodology is a **structured way to gather information about a target system** during a penetration test.
>
> 👉 It ensures:
>
> * Nothing is missed
> * Steps are repeatable
> * Investigation stays organized
>
> ❗ It is NOT a fixed checklist — it is a **flexible framework**

---

> [!tip] 🧭 Core Idea
> Think of enumeration as a **layered investigation process**:
>
> ```text
> Internet → Gateway → Services → Processes → Privileges → OS
> ```
>
> Each layer helps you move deeper into the system.

---

# 🧱 The 6 Layers of Enumeration

---

> [!info] 🌍 Layer 1: Internet Presence
> 🎯 Goal: Find everything exposed on the internet
>
> **What you look for:**
>
> * Domains & subdomains
> * IP addresses
> * Netblocks & ASN
> * Cloud assets
> * Public services
>
> 👉 This is your **target discovery phase**

---

> [!warning] 🛡️ Layer 2: Gateway
> 🎯 Goal: Understand how the network is protected
>
> **What you look for:**
>
> * Firewalls
> * VPNs
> * Proxies
> * IDS/IPS
> * Network segmentation
>
> 👉 This tells you: *“How hard is it to enter?”*

---

> [!info] 🔌 Layer 3: Accessible Services
> 🎯 Goal: Identify exposed services and interfaces
>
> **What you look for:**
>
> * Open ports
> * Service type (SMB, HTTP, RDP, SSH, etc.)
> * Versions
> * Configurations
>
> 👉 This is the **main attack surface**

---

> [!tip] ⚙️ Layer 4: Processes
> 🎯 Goal: Understand what is running internally
>
> **What you look for:**
>
> * Running processes (PID)
> * Data flow (source → destination)
> * Scheduled tasks
>
> 👉 This shows **how the system behaves**

---

> [!warning] 🔐 Layer 5: Privileges
> 🎯 Goal: Identify what users/services are allowed to do
>
> **What you look for:**
>
> * User accounts
> * Group memberships
> * Permissions
> * Misconfigurations
>
> 👉 This is where **privilege escalation paths appear**

---

> [!info] 🖥️ Layer 6: OS Setup
> 🎯 Goal: Understand the system environment
>
> **What you look for:**
>
> * OS version
> * Patch level
> * Network config
> * Sensitive files
>
> 👉 This reveals **system weaknesses and misconfigurations**

---

# 🧠 How the Method Works in Practice

> [!tip] 🔁 Enumeration is NOT linear
>
> You don’t go:
>
> ```text
> Layer 1 → Layer 2 → Layer 3 → Done
> ```
>
> Instead:
>
> ```text
> Discover → Re-analyze → Expand → Repeat
> ```

👉 It is a **looping discovery process**

---

# 🧩 Key Concept: “Walls & Gaps” Analogy

> [!quote] 🧱 Think of it like a maze
>
> * Each layer = a wall
> * Each vulnerability = a gap
>
> ❌ Not all gaps lead forward
> ❌ Some paths are dead ends
>
> 👉 The skill is finding the **right entry point, not forcing every wall**

---

# ❌ Common Mistake

> [!danger] brute force first mindset
>
> Many beginners:
>
> * Try password attacks too early
> * Skip understanding infrastructure
>
> Result:
>
> * Detection
> * Blocking
> * Wasted effort
>
> 👉 Correct approach = **map first, attack later**

---

# 🧠 Core Principles

> [!important]
>
> 1. Methodology ≠ toolset
> 2. Tools change, structure stays the same
> 3. Understanding > guessing
> 4. Every discovery leads to another layer

---

# 🔥 Final Mental Model

```text
Discovery → Categorize → Analyze → Expand → Repeat → Exploit Path Found
```

---

If you want, I can next turn this into:

* a **real HTB workflow (SMB → RDP → WinRM path mapping)**
* or a **visual cheat sheet you can revise before exams/interviews**


