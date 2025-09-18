
---

# 🗂️ `waybackpy` Cheat Sheet (CLI)

## 📌 Installation

```bash
pip install waybackpy
```

---

## 📌 Core Options

- `-u, --url` → Target URL.
    
- `-ua, --user-agent` → Custom user agent.
    
- `-n, -au, --newest, --archive-url` → Get **newest snapshot**.
    
- `-o, --oldest` → Get **oldest snapshot**.
    
- `-Y, --year` → Snapshot from a specific **year**.
    
- `-M, --month` → From a specific **month**.
    
- `-D, --day` → From a specific **day**.
    
- `-H, --hour` → From a specific **hour**.
    
- `-MIN, --minute` → From a specific **minute**.
    
- `-s, --save` → Save a live page to Wayback.
    
- `-ku, --known-urls` → List known URLs (CDX API).
    
- `-sub, --subdomain` → Include subdomains (use with `--known-urls`).
    
- `-f, --file` → Save URLs to file.
    
- `-st, --from` → Start timestamp (`yyyyMMddhhmmss`).
    
- `-et, --to` → End timestamp (`yyyyMMddhhmmss`).
    
- `-C, --closest` → Snapshot closest to timestamp.
    
- `-cp, --cdx-print` → Print specific CDX API fields.
    
- `-mt, --match-type` → Match prefix/host/subdomains.
    
- `-c, --collapse` → Collapse/unique filter.
    
- `-l, --limit` → Limit results (default: 25k).
    

---

## 📌 Practical Usage

### 🔹 Get newest snapshot of a site

```bash
waybackpy -u example.com -n
```

### 🔹 Get oldest snapshot

```bash
waybackpy -u example.com -o
```

### 🔹 Get snapshot from a specific year/month

```bash
waybackpy -u example.com -Y 2018 -M 5
```

### 🔹 Save live version to Wayback

```bash
waybackpy -u https://example.com -s
```

### 🔹 List all known URLs for a site

```bash
waybackpy -u example.com -ku
```

### 🔹 List known URLs (including subdomains)

```bash
waybackpy -u example.com -ku -sub
```

### 🔹 Save known URLs to file

```bash
waybackpy -u example.com -ku -f
```

### 🔹 List known URLs between specific dates

```bash
waybackpy -u example.com -ku -st 20180101 -et 20191231
```

### 🔹 Get snapshot closest to a date

```bash
waybackpy -u example.com -C 20200101
```

---

## 📌 Recon / Pentesting Tricks

🔍 **Extract all admin panels from history**

```bash
waybackpy -u target.com -ku | grep "admin"
```

📂 **Download historical robots.txt**

```bash
waybackpy -u target.com/robots.txt -ku -f
```

🕵️ **Look for old API endpoints**

```bash
waybackpy -u api.target.com -ku | grep "v1"
```

⚡ **Use in fuzzing pipeline**

```bash
waybackpy -u target.com -ku | ffuf -u https://target.com/FUZZ -w -
```

---

# ✅ Quick Reference

- **Newest snapshot** → `-n`
    
- **Oldest snapshot** → `-o`
    
- **Specific year/month/day** → `-Y`, `-M`, `-D`
    
- **Save live version** → `-s`
    
- **List known URLs** → `-ku`
    
- **Include subdomains** → `-sub`
    
- **Filter by date range** → `-st`, `-et`
    
- **Collapse duplicates** → `-c`
    
- **Limit results** → `-l`
    

---

