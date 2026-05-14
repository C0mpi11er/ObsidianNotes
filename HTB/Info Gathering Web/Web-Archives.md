
---

# 🕰️ Web Archives — Wayback Machine Recon Cheat Sheet

> [!ABSTRACT]  
> The Wayback Machine (Internet Archive) is a historical web snapshot system that stores past versions of websites. In reconnaissance, it is used to recover deleted assets, discover hidden endpoints, and analyze historical infrastructure changes.

---

## 🧠 What is the Wayback Machine?

> [!INFO]  
> The Wayback Machine is a digital archive of the web maintained by the Internet Archive.

```txt
https://web.archive.org/
```

It stores:

- 🌐 HTML pages
    
- 🧩 JavaScript / CSS assets
    
- 🖼️ Images
    
- 📁 Full historical site structure snapshots
    

---

## ⚙️ How It Works

> [!NOTE]

### 1. Crawling

Automated bots scan websites and follow links.

### 2. Archiving

Snapshots are stored with timestamps:

```txt
/web/20240101000000/https://example.com/
```

### 3. Accessing

Users retrieve historical versions via URL-based time selection.

---

## 🧠 Why It Matters in Recon

> [!IMPORTANT]

Wayback provides **time-based attack surface intelligence**:

- 🧱 Deleted endpoints still accessible
    
- 🔐 Old admin panels / login pages
    
- 📁 Exposed files no longer linked
    
- 🧪 Legacy APIs and parameters
    
- 🧠 Historical app behavior changes
    

---

## 🔎 Core Recon Use Cases

> [!CHECK]

### 1. Hidden Endpoint Discovery

- `/admin/`
    
- `/backup/`
    
- `/test/`
    
- `/old/`
    

### 2. Parameter Recon

- Old GET/POST parameters
    
- Deprecated API endpoints
    

### 3. Sensitive File Recovery

- `.zip`, `.bak`, `.sql`, `.env`
    

### 4. Tech Stack History

- CMS changes (WordPress → custom app)
    
- Framework migrations
    
- Deprecated JS libraries
    

---

## 🧭 Accessing Wayback Data

> [!EXAMPLE]

### Manual Search

```txt
https://web.archive.org/web/*/example.com
```

---

### Oldest Snapshot

Navigate to earliest capture:

```txt
https://web.archive.org/web/19960101000000*/example.com
```

---

## ⚡ Recon Techniques

> [!TIP]

### 1. List All Captured URLs

Use Wayback CDX API:

```bash
curl "https://web.archive.org/cdx/search/cdx?url=example.com/*&output=text"
```

---

### 2. Extract Unique Endpoints

```bash
curl "https://web.archive.org/cdx/search/cdx?url=example.com/*&output=text&fl=original&collapse=urlkey"
```

---

### 3. Filter for Sensitive Files

```bash
curl "https://web.archive.org/cdx/search/cdx?url=example.com/*.sql&output=text"
```

---

## 🧨 What Attackers Commonly Find

> [!WARNING]

- Forgotten `/admin` portals
    
- Exposed `.git` or backup archives
    
- Old login systems without MFA
    
- Hardcoded API keys in JS files
    
- Legacy authentication endpoints
    

---

## 🧠 Recon Value Position

> [!INFO]

```txt
DNS → Subdomains → CT Logs → VHosts → Crawling → Search Engines → Wayback Machine → Fingerprinting
```

---

## 💡 Key Insight

> [!TLDR]  
> Wayback Machine = **time-based recon engine that reveals what the target tried to erase**

---

If you want next step, I can upgrade your full system into:

- 🧠 **Elite OSINT Recon Pipeline (complete methodology map)**
    
- ⚡ **One-liner Wayback API + grep + jq recon automation toolkit**
    
- 🧨 **Real-world “hidden endpoint recovery” workflow (CTF + red team style)**