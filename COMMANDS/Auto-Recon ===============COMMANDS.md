Absolutely — the best way is to turn your notes into a **practical AutoRecon cheat sheet that includes normal scans + pivot/tunnel-safe scans**, because using AutoRecon through Ligolo-ng or proxy tunnels needs a different approach from direct scanning.

# 🚀 AutoRecon Cheat Sheet (Standard + Tunnel/Pivot Mode)

---

# 🔥 1. Standard Fast Recon (Direct Target)

For HTB / labs when you're directly connected.

```bash
autorecon 10.10.10.15 -o autorecon_results
```

> **Use when**

* Direct VPN connection
* No pivoting
* Fast broad recon needed

### Default behavior

* Full TCP discovery
* Service detection
* Plugin execution
* Web enum
* SMB enum
* Report generation

---

# ⚡ 2. Aggressive CTF Mode

Faster but noisier.

```bash
autorecon 10.10.10.15 \
-m 50 \
--max-port-scans 10 \
--timeout 30 \
-o fast_scan
```

> **Best for**

* CTFs
* OSCP labs
* Non-sensitive environments

---

# 🛡 3. Controlled Reliable Recon

More accurate, less noisy.

```bash
autorecon 10.10.10.15 \
-m 15 \
--max-port-scans 4 \
--nmap "-sS -sV -Pn -T3 --max-retries 2" \
-o controlled_scan
```

> Better for:

* Slower targets
* Less packet loss
* Better service accuracy

---

# 🌐 4. Tunnel / Pivot Safe Scan (Ligolo / Chisel / SOCKS)

This is the important one.

```bash
autorecon 10.10.20.15 \
-m 5 \
--max-port-scans 1 \
--target-timeout 10 \
--timeout 20 \
--nmap "-sT -sV -Pn -T2 --max-retries 1" \
--exclude-tags brute,dirbuster \
-o tunnel_scan
```

> **Why this works better over tunnels**

* `-sT` works better than SYN through tunnels
* low concurrency avoids packet loss
* avoids overwhelming pivot host
* prevents broken plugin scans

---

## 🔥 Why `-sT` instead of `-sS`

Through tunnels:

❌ Bad:

```bash
-sS
```

✅ Better:

```bash
-sT
```

Because:

* tunnels handle TCP connect scans better
* SYN scans may fail
* more stable through Ligolo

---

# 🎯 5. Target Specific Ports Only

Skip discovery if you already know ports.

```bash
autorecon 10.10.10.15 -p 80,135,445,3389
```

Useful after manual Nmap.

---

# 🧠 6. Plugin Selection

Run only required modules.

```bash
autorecon 10.10.10.15 --tags default,web,smb
```

Examples:

* `web`
* `smb`
* `snmp`
* `ldap`

---

# 🚫 7. Exclude Noisy Modules

Avoid crashing tunnel scans.

```bash
autorecon 10.10.20.15 --exclude-tags brute,dirbuster
```

Recommended for:

* pivots
* slow networks
* stealthier engagements

---

# 🔥 8. Force Enumeration if Detection Fails

When Nmap identifies incorrectly.

```bash
autorecon 10.10.10.15 \
--force-services tcp/80/http tcp/445/microsoft-ds
```

Format:

```bash
tcp/PORT/SERVICE
```

---

# ⚙ 9. Full Custom Nmap

Override defaults.

```bash
autorecon 10.10.10.15 \
--nmap "-sV -sC -p- -T4 --min-rate 1500"
```

Useful if you already have your own Nmap style.

---

# 📂 10. Clean Output Structure

```bash
autorecon 10.10.10.15 \
-o results \
--only-scans-dir \
--no-port-dirs
```

Keeps output cleaner.

---

# 🔎 11. Best Workflow for Tunnel Scans

---

## Step 1 – verify tunnel

Ligolo route:

```bash
ip route
```

Should show:

```bash
10.10.20.0/24 dev ligolo
```

---

## Step 2 – quick port scan first

```bash
nmap -Pn -sT -p- --min-rate 300 10.10.20.15
```

---

## Step 3 – feed ports into AutoRecon

```bash
autorecon 10.10.20.15 -p 80,445,3389
```

This avoids slow full scans through tunnel.

---

# 🔗 12. Useful Ligolo Commands During Recon

Using Ligolo-ng:

## Show agents

```bash
session
```

---

## Select agent

```bash
session 1
```

---

## Start tunnel

```bash
start
```

---

## Add route

```bash
sudo ip route add 10.10.20.0/24 dev ligolo
```

---

## Test target

```bash
nc -vz 10.10.20.15 445
```

or

```bash
curl http://10.10.20.15
```

---

# 🌍 13. Manual Web Enum Through Tunnel

Safer than plugin enum.

```bash
ffuf -u http://10.10.20.15/FUZZ \
-w /usr/share/seclists/Discovery/Web-Content/common.txt \
-t 10
```

Keep threads low:

```bash
-t 10
```

Not:

```bash
-t 50
```

because tunnels drop packets.

---

# 🪟 14. SMB Through Tunnel

```bash
smbclient -L //10.10.20.15 -N
```

or

```bash
crackmapexec smb 10.10.20.15 --timeout 15
```

---

# ⚠ Common Tunnel Problems

---

## Problem: Missing ports

Cause:

* scan too aggressive

Fix:

```bash
-sT -T2 -m 5
```

---

## Problem: Web enum hangs

Cause:

* dirbuster too many threads

Fix:

```bash
--exclude-tags dirbuster
```

Then run ffuf manually.

---

## Problem: Service detection wrong

Fix:

```bash
--force-services
```

---

# 🏆 Best Tunnel-Safe AutoRecon Command

My recommended profile:

```bash
autorecon 10.10.20.15 \
-p 80,445,3389 \
-m 5 \
--max-port-scans 1 \
--nmap "-sT -sV -Pn -T2 --max-retries 1" \
--exclude-tags brute,dirbuster \
-o pivot_scan
```

---

# 💡 Best Practice

AutoRecon should be:

✅ assistant
❌ your brain

Always combine with:

* manual Nmap
* manual web checks
* service-specific tools

---

