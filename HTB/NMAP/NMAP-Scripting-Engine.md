# Nmap Scripting Engine (NSE)

The **Nmap Scripting Engine (NSE)** is one of Nmap's most powerful features, allowing users to automate networking tasks using **Lua** scripts. These scripts can perform everything from advanced service discovery to full-scale vulnerability exploitation.

---

## 📂 NSE Script Categories

Nmap scripts are organized into 14 categories based on their function and "noise" level (how likely they are to be detected or cause a crash).

|Category|Description|
|---|---|
|`default`|Safe, fast scripts run with `-sC`.|
|`auth`|Checks for authentication/credentials on services.|
|`discovery`|Actively tries to map the network and find hidden services.|
|`vuln`|Scans for specific known vulnerabilities (e.g., CVEs).|
|`exploit`|Attempts to actively exploit a vulnerability.|
|`brute`|Attempts to brute-force passwords for services.|
|`dos`|Checks for Denial of Service vulnerabilities (use with caution).|
|`intrusive`|High risk of crashing services or being caught by IDS.|
|`malware`|Checks for signs of infection or backdoors.|
|`safe`|Won't crash services or use excessive bandwidth.|

---

## 🚀 Running Scripts

### 1. The "Aggressive" Combo (`-A`)

The `-A` flag is a "shortcut" for comprehensive scanning. It enables:

- **Service Detection** (`-sV`)
    
- **OS Detection** (`-O`)
    
- **Traceroute** (`--traceroute`)
    
- **Default Scripts** (`-sC`)
    

### 2. Specific Vulnerability Assessment

By using the `--script vuln` category, Nmap checks the identified service versions against databases of known vulnerabilities.

**Example Insight from your scan:**

- **Service:** Apache 2.4.29
    
- **NSE Result:** Found **CVE-2019-0211** (a major local privilege escalation bug).
    
- **Application:** WordPress 5.3.4 (Identified sensitive files like `/wp-login.php` and the username `admin`).
    

---

## 💻 Command Reference (Copy & Paste)

### Script Selection

Bash

```
# Run all 'default' scripts (Safe and Recommended)
sudo nmap <target> -sC

# Run all scripts in a specific category (e.g., Vulnerability check)
sudo nmap <target> --script vuln

# Run specific scripts by name
sudo nmap <target> -p 25 --script banner,smtp-commands

# Run multiple categories
sudo nmap <target> --script="default or safe"
```

### Advanced Discovery

Bash

```
# Aggressive scan (OS, Versions, Scripts, Traceroute)
sudo nmap <target> -A

# Enumerating WordPress users and versions via HTTP
sudo nmap <target> -p 80 -sV --script http-wordpress-users,http-enum
```

---

**Tags:** #nmap #NSE #vulnerability-scanning #pentesting #wordpress-security #automation

> [!WARNING] Be careful with `dos`, `exploit`, and `intrusive` scripts. These can knock services offline or trigger aggressive security alerts on a professional network. Always ensure you have permission before running these categories.

