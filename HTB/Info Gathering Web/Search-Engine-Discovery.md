

---

# 🔎 Search Engine Discovery (OSINT Recon Cheat Sheet)

> [!ABSTRACT]  
> Search Engine Discovery (OSINT) is the process of using search engines as structured intelligence sources to uncover exposed assets, sensitive files, and hidden endpoints indexed on the public web.

---

## 🧠 Why It Matters

> [!INFO]  
> Search engines index **unexpected exposure points**, including:

- Hidden admin panels
    
- Leaked documents
    
- Backup files
    
- Internal paths
    
- Misconfigured directories
    

### Key advantage:

```txt
No direct target interaction → stealth recon
```

---

## ⚙️ Core Value in Recon

> [!IMPORTANT]

- 🌍 Massive passive data source (Google, Bing, etc.)
    
- 🔍 Finds content not linked on site UI
    
- 🧠 Reveals historical / forgotten assets
    
- 💸 Zero cost, zero footprint
    

---

## 🧩 Search Operators (Core Toolkit)

> [!NOTE]

### 📌 Essential Operators

|Operator|Function|Example|
|---|---|---|
|`site:`|Restrict to domain|`site:example.com`|
|`inurl:`|Match URL keyword|`inurl:admin`|
|`intitle:`|Match page title|`intitle:login`|
|`filetype:`|Find file types|`filetype:pdf`|
|`intext:`|Search page content|`intext:"password"`|

---

### 📌 Advanced Operators

|Operator|Function|Example|
|---|---|---|
|`cache:`|View cached page|`cache:example.com`|
|`related:`|Find similar sites|`related:example.com`|
|`link:`|Backlinks|`link:example.com`|
|`numrange:`|Numeric filtering|`numrange:100-500`|
|`allinurl:`|Multiple URL terms|`allinurl:admin panel`|
|`allintitle:`|Multiple title terms|`allintitle:confidential report`|

---

### 📌 Boolean Logic

> [!TIP]

```txt
AND  → must include all terms
OR   → include any term
NOT  → exclude term
```

Example:

```txt
site:example.com AND (login OR admin)
```

---

## 🔥 Google Dorking (Attack-Focused OSINT)

> [!WARNING]  
> Google Dorking can expose sensitive assets unintentionally indexed by search engines.

---

### 🔐 Login Pages

```txt
site:example.com inurl:login
site:example.com (inurl:login OR inurl:admin)
```

---

### 📁 Exposed Files

> [!EXAMPLE]

```txt
site:example.com filetype:pdf
site:example.com filetype:xls OR filetype:docx
```

---

### ⚙️ Config Files

```txt
site:example.com inurl:config
site:example.com (ext:conf OR ext:cnf OR ext:env)
```

---

### 💾 Backups & Databases

```txt
site:example.com inurl:backup
site:example.com filetype:sql
```

---

### 🧠 Sensitive Content Discovery

```txt
site:example.com intext:"password"
site:example.com intext:"internal use only"
```

---

## 🧭 Recon Use Cases

> [!CHECK]

Search engine discovery is used for:

- 🔓 Exposed credentials / files
    
- 🧾 Document leakage (PDFs, spreadsheets)
    
- 🧑‍💻 Admin panel discovery
    
- 🧱 Hidden endpoints
    
- 🧠 Employee / org intelligence
    
- 🕵️ Historical content recovery
    

---

## ⚖️ Limitations

> [!CAUTION]

- Not all pages are indexed
    
- Robots.txt may block crawling
    
- Sensitive systems may be de-indexed
    
- Results may be outdated (cached snapshots)
    

---

## 🧠 Recon Position in Pipeline

> [!INFO]

```txt
DNS → Subdomains → CT Logs → VHosts → Crawling → Search Engine Discovery → Fingerprinting
```

---

## 💡 Key Insight

> [!TLDR]  
> Search engines act as a **pre-built reconnaissance index of the internet**, often revealing assets the target forgot existed.

---

If you want, I can next:

- ⚡ Turn this into a **Google Dorking attack playbook (real-world OSINT combos)**
    
- 🧠 Or merge everything you’ve built into a **single master Web Recon OSINT handbook (elite level)**