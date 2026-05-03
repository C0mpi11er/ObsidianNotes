

---

# 🌐 Domain Information Cheat Sheet

---

> [!info] 🧠 What is Domain Information?
>
> ```bash
> Entire internet presence of a company
> ```
>
> 💡 Not just subdomains — includes:
>
> * Domains / subdomains
> * IP addresses
> * services & technologies
> * third-party integrations

---

> [!tip] 🕵️ Passive Recon (Golden Rule)
>
> ```bash
> No scans. No touching target directly.
> ```
>
> 💡 Act like:
>
> * Visitor
> * Customer
> * Normal user
>
> ✔ Stay hidden
> ✔ Avoid detection

---

> [!info] 🔎 Start with Main Website
>
> ```bash
> http://target.com
> ```
>
> 💡 Look for:
>
> * Services offered
> * Tech hints (APIs, portals)
> * Emails
> * Subdomains
>
> ✔ This gives your **first attack surface map**

---

> [!tip] 🧠 Think Like a Developer
>
> ```bash
> "What must exist behind this service?"
> ```
>
> 💡 Example:
>
> * IoT → APIs + devices + cloud
> * Hosting → servers + DNS
> * App dev → repos + CI/CD
>
> ✔ You infer what is **hidden**

---

# 🔐 SSL Certificate Enumeration

---

> [!success] 📜 Extract Domains from SSL
>
> ```bash
> openssl s_client -connect target.com:443
> ```
>
> 💡 Certificates often contain:
>
> * multiple subdomains
> * internal naming patterns

---

> [!tip] 🌐 Use Certificate Transparency
>
> Use crt.sh
>
> ```bash
> curl -s https://crt.sh/?q=target.com&output=json | jq .
> ```
>
> 💡 Finds:
>
> * hidden subdomains
> * historical domains

---

> [!success] 🧾 Extract Unique Subdomains
>
> ```bash
> curl -s https://crt.sh/?q=target.com&output=json | jq . \
> | grep name | cut -d":" -f2 | cut -d'"' -f2 \
> | sort -u
> ```
>
> 💡 Builds clean subdomain list

---

# 🌍 Resolve Infrastructure

---

> [!info] 🔎 Convert Domains → IPs
>
> ```bash
> for i in $(cat subs.txt); do host $i; done
> ```
>
> 💡 Helps you:
>
> * identify real servers
> * separate third-party hosts

---

> [!warning] ⚠️ Important Rule
>
> ```bash
> DO NOT test third-party infrastructure
> ```
>
> 💡 Only test:
>
> * company-owned assets

---

# 🔍 Internet Intelligence

---

> [!tip] 🌐 Scan IPs with Shodan
>
> Use Shodan
>
> ```bash
> shodan host <IP>
> ```
>
> 💡 Reveals:
>
> * open ports
> * services
> * versions

---

> [!success] 🎯 What to Look For
>
> ```bash
> 22 → SSH
> 80/443 → Web servers
> 25 → Mail
> ```
>
> 💡 Helps identify:
>
> * attack vectors
> * service exposure

---

# 🧬 DNS Enumeration

---

> [!info] 🔎 Dump DNS Records
>
> ```bash
> dig any target.com
> ```
>
> 💡 Returns:
>
> * A, MX, NS, TXT records

---

> [!tip] 🧾 Important Record Types
>
> ```bash
> A   → IP mapping
> MX  → Mail servers
> NS  → Name servers
> TXT → Hidden intelligence
> ```
>
> 💡 TXT records are **VERY valuable**

---

# 🔥 TXT Records = Intelligence Goldmine

---

> [!success] 🧠 Extract Hidden Services
>
> ```bash
> dig txt target.com
> ```
>
> 💡 Can reveal:
>
> * APIs
> * email providers
> * cloud services
> * verification tokens

---

> [!tip] 🧩 Example Insights
>
> ```bash
> Atlassian → dev tools
> Google → email / drive
> Outlook → Microsoft ecosystem
> Mailgun → email API
> LogMeIn → remote access
> ```
>
> 💡 These = **new attack surfaces**

---

# 🧠 Enumeration Mindset

---

> [!quote] 🎯 Think Like This
>
> ```bash
> What do I see?
> What don’t I see?
> Why is it there?
> How can I use it?
> ```
>
> 💡 The hidden parts matter most

---

> [!warning] ⚠️ Beginner Mistake
>
> ```bash
> Jumping straight to brute force
> ```
>
> ❌ Noisy
> ❌ Gets blocked
> ❌ Poor methodology

---

> [!success] ✔ Correct Approach
>
> ```bash
> Understand → Map → Then Attack
> ```

---

# ⚡ Real-World Workflow

---

> [!success] 🧠 Domain Recon Flow
>
> ```bash
> 1. Analyze website
> 2. Extract SSL domains
> 3. Query crt.sh
> 4. Build subdomain list
> 5. Resolve to IPs
> 6. Check with Shodan
> 7. Dump DNS records
> 8. Analyze TXT records
> ```
>
> 💡 Build full infrastructure map

---

# 🧩 Mental Model

---

> [!quote] 🎯 Think Like This
>
> ```bash
> DOMAIN → SUBDOMAINS → IPs → SERVICES → TECHNOLOGIES → ATTACK SURFACE
> ```

---

> [!tip] 🚀 Pro Tips
>
> * Always start passive
> * Don’t trust surface-level info
> * TXT records = hidden gold
> * Think like developer, not hacker
> * Build a map before attacking

---

If you want next, I can:

* 🔥 Turn this into a **real HTB lab execution (exact commands for inlanefreight)**
* 🧪 Or combine this with your **enumeration + exploitation path (full chain)**

