

---

### 🚀 1. The "Fire-and-Forget" Default Scan (Baseline Enumeration)

Your go-to when you land on a box and want **broad automated coverage fast**.

Bash

```
autorecon 10.10.10.15 -o autorecon_results
```

> [!ABSTRACT] **What AutoRecon Actually Does**
> 
> - Runs **Nmap port scans first**, then chains **service-specific enumeration**
>     
> - Automatically launches tools like:
>     
>     - Web → dir busting (feroxbuster/ffuf)
>         
>     - SMB → enum4linux
>         
>     - SNMP → onesixtyone
>         
> - Organizes output into structured folders (**scans / loot / report**)
>     

> [!NOTE] **Default Behavior**
> 
> - Uses built-in **plugin system** (tag-based execution)
>     
> - Default Nmap flags:
>     
>     ```
>     -vv --reason -Pn -T4
>     ```
>     
> - Parallel execution = **fast but noisy**
>     

---

### ⚡ 2. The "Blitz Recon" (CTF / OSCP Speed Run)

Max speed, minimal patience—great for labs where stealth is irrelevant.

Bash

```
autorecon 10.10.10.15 -m 100 --max-port-scans 20 --timeout 30 -o fast_scan
```

> [!DANGER] **Aggression Profile**
> 
> - `-m 100`: Massive parallelism (default 50 → doubled)
>     
> - `--max-port-scans 20`: More simultaneous Nmap scans
>     
> - `--timeout 30`: Kill scan after 30 mins (don’t get stuck)
>     
> 
> ⚠️ Expect:
> 
> - Dropped packets
>     
> - Missed services
>     
> - Loud traffic signature
>     

---

### 🕵️ 3. The "Controlled Recon" (Real Engagement Mode)

When you want **clean, reliable results without flooding the network**.

Bash

```
autorecon 10.10.10.15 -m 20 --max-port-scans 5 --nmap "-T3 -sS -Pn --max-retries 2" -o controlled_scan
```

> [!WARNING] **Stealth Trade-offs**
> 
> - `-m 20`: Reduces concurrency → less noise
>     
> - `--nmap`: Overrides default aggressive flags
>     
> - `-T3`: Balanced timing (safer than T4)
>     
> 
> ⚠️ Slower, but:
> 
> - More accurate results
>     
> - Less IDS/IPS attention
>     

---

### 🎯 4. Targeted Port / Service Recon

When you **already know ports** and want to skip full discovery.

Bash

```
autorecon 10.10.10.15 -p 80,443,22
```

> [!NOTE] **Precision Mode**
> 
> - `-p`: Skip full port scan → go straight to enumeration
>     
> - Supports:
>     
>     - TCP: `T:80,443`
>         
>     - UDP: `U:53`
>         
>     - Both: `B:53`
>         

---

### 🧠 5. Plugin Control (Selective Enumeration)

Control exactly **what AutoRecon runs**.

Bash

```
autorecon 10.10.10.15 --tags default,web
```

> [!ABSTRACT] **Plugin Tag Logic**
> 
> - Plugins are grouped by tags:
>     
>     - `default` → core scans
>         
>     - `web` → HTTP enumeration
>         
>     - `smb`, `snmp`, etc.
>         
> - Logic:
>     
>     - `+` → AND condition
>         
>     - `,` → OR condition
>         

---

### 🚫 6. Excluding Noisy or Useless Scans

Avoid wasting time or triggering defenses.

Bash

```
autorecon 10.10.10.15 --exclude-tags brute,dirbuster
```

> [!CAUTION] **Why This Matters**
> 
> - Dirbusting can:
>     
>     - Be extremely noisy
>         
>     - Take hours
>         
> - Bruteforce plugins:
>     
>     - Risk lockouts
>         
>     - Trigger alerts
>         

---

### 🌐 7. Web Enumeration Customization (Dirbuster Control)

Fine-tune web fuzzing aggressively.

Bash

```
autorecon 10.10.10.15 \
--dirbuster.tool ffuf \
--dirbuster.wordlist /usr/share/seclists/Discovery/Web-Content/common.txt \
--dirbuster.threads 50 \
--dirbuster.ext php,html,txt \
--dirbuster.recursive
```

> [!WARNING] **Web Fuzzing Impact**
> 
> - `threads 50` = very aggressive
>     
> - `recursive` = exponential scan time
>     
> 
> ⚠️ Can:
> 
> - Crash fragile apps
>     
> - Trigger WAF bans
>     

---

### 🧩 8. Forcing Service Enumeration (Bypass Detection Failures)

When AutoRecon **misses a service**.

Bash

```
autorecon 10.10.10.15 --force-services tcp/80/http tcp/443/https
```

> [!ATTENTION] **Critical Use Case**
> 
> - If Nmap misidentifies a service → plugins won’t run
>     
> - This forces execution anyway
>     
> 
> Format:
> 
> ```
> tcp/port/service
> ```

---

### 🧪 9. Manual Nmap Override (Full Control Mode)

Take control of scanning behavior completely.

Bash

```
autorecon 10.10.10.15 --nmap "-sS -sV -p- -T4 --min-rate 2000"
```

> [!TIP] **Operator Mode**
> 
> - Overrides ALL default Nmap flags
>     
> - Combine with your custom Nmap methodology
>     
> 
> 💡 Best practice:
> 
> - Use your proven Nmap profiles inside AutoRecon
>     

---

### 🧬 10. Output Control & Loot Management

Keep your workspace clean and efficient.

Bash

```
autorecon 10.10.10.15 -o results --only-scans-dir --no-port-dirs
```

> [!NOTE] **Output Structure**
> 
> Default:
> 
> ```
> results/
> ├── scans/
> ├── loot/
> ├── report/
> ```
> 
> Options:
> 
> - `--only-scans-dir`: Skip loot/report folders
>     
> - `--no-port-dirs`: Flatten structure
>     

---

### ⏱️ 11. Time-Bound Engagements

Prevent wasting time on dead targets.

Bash

```
autorecon 10.10.10.15 --target-timeout 20 --timeout 60
```

> [!CHECK] **Time Control**
> 
> - `--target-timeout 20`: Per-target limit (minutes)
>     
> - `--timeout 60`: Global scan limit
>     

---

### 🔗 12. Proxy / Pivoting Mode

For internal network pivots.

Bash

```
proxychains autorecon 10.10.10.15 --proxychains
```

> [!WARNING] **Pivot Reality**
> 
> - VERY slow
>     
> - Many plugins will break or timeout
>     
> 
> 💡 Reduce:
> 
> - Threads
>     
> - Parallel scans
>     

---

### 📂 Quick Reference: High-Value Flags

|Flag|Purpose|
|---|---|
|`-m`|Max concurrent scans|
|`--max-port-scans`|Parallel Nmap scans|
|`-p`|Target specific ports|
|`--nmap`|Override scan flags|
|`--tags`|Select plugins|
|`--exclude-tags`|Skip plugins|
|`--force-services`|Force enum|
|`-o`|Output directory|

---

### 💡 Pro-Tips (Real Operator Notes)

> [!QUESTION] **AutoRecon missed a service I KNOW exists?**
> 
> **Fix:**
> 
> - Run manual Nmap
>     
> - Then:
>     
> 
> ```
> --force-services tcp/PORT/service
> ```

---

> [!FAILURE] **AutoRecon is too slow / stuck**
> 
> Cause:
> 
> - Recursive dirbusting
>     
> - Too many plugins
>     
> 
> Fix:
> 
> ```
> --exclude-tags dirbuster
> --timeout 30
> ```

---

> [!ATTENTION] **Blind trust = missed foothold**
> 
> AutoRecon is:
> 
> - Automation ≠ intelligence
>     
> 
> ALWAYS:
> 
> - Manually review:
>     
>     - Nmap output
>         
>     - Web responses
>         
>     - Odd ports
>         

---

> [!SUCCESS] **Best Workflow (OSCP/CTF)**
> 
> 1. Run:
>     
> 
> ```
> autorecon <target> &
> ```
> 
> 2. While it runs:
>     
> 
> - Manual Nmap
>     
> - Quick web check
>     
> - Enum interesting ports
>     
> 
> 3. Use AutoRecon results as:
>     
> 
> - **Parallel recon assistant**, not primary brain
>     

---

If you want next-level refinement, I can:

- Integrate this with your **Nmap + manual enum workflow**
    
- Add **service-specific follow-ups (SMB, LDAP, Web, etc.)**
    
- Or convert this into a **full OSCP methodology playbook in your exact Obsidian style**