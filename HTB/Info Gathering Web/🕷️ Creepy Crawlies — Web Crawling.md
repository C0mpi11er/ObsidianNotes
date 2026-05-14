> [!ABSTRACT]  
> Web crawling (spidering) is the automated discovery of web content by recursively following links. In reconnaissance, it is used to map application structure, extract assets, and uncover hidden attack surfaces.

---

## 🧠 What Crawlers Do

> [!INFO]  
> A crawler:

- Starts from a seed URL
- Extracts all links on a page
- Follows links recursively
- Builds a structured map of the site

```
Seed URL → Page → Links → More Pages → Full Site Map
```

---

## 🧰 Common Crawling Tools

> [!NOTE]

|Tool|Type|Strength|
|---|---|---|
|Burp Suite Spider|Active crawler|Deep app mapping + auth-aware crawling|
|OWASP ZAP|Proxy + crawler|Automated security + spider integration|
|Scrapy|Python framework|Fully customizable crawling logic|
|Apache Nutch|Scalable crawler|Large-scale / enterprise crawling|

---

## ⚖️ Ethical Constraint

> [!WARNING]  
> Crawlers can generate high traffic. Always:

- Respect rate limits
- Avoid production disruption
- Obtain authorization before large-scale crawling

---

## 🧪 Scrapy Setup (Recon Mode)

> [!EXAMPLE]

### Install Scrapy

```
pip3 install scrapy
```

---

### Run Recon Spider

```
python3 ReconSpider.py http://target.com
```

---

## 📦 ReconSpider Output Structure

> [!ABSTRACT]  
> Output is stored in `results.json`, structured into categorized reconnaissance data.

```
{  "emails": [],  "links": [],  "external_files": [],  "js_files": [],  "form_fields": [],  "images": [],  "videos": [],  "audio": [],  "comments": []}
```

---

## 🔍 Data Types Explained

> [!CHECK]

|Key|Recon Value|
|---|---|
|`emails`|User/org enumeration targets|
|`links`|Site structure mapping|
|`external_files`|Leaked PDFs, docs, backups|
|`js_files`|API endpoints, hidden logic|
|`form_fields`|Input surfaces for injection testing|
|`images`|Metadata leaks / hidden paths|
|`comments`|Developer notes, secrets, hints|

---

## 🧠 Recon Value of Crawling

> [!IMPORTANT]  
> Crawling is not just “link collection” — it enables:

- 🗺️ Full application mapping
- 🔐 Hidden endpoint discovery
- 📁 Sensitive file exposure
- 🧩 JavaScript-based API extraction
- 🧠 Context correlation (comments + endpoints + assets)

---

## ⚙️ Typical Recon Workflow

> [!TIP]

```
robots.txt → .well-known → DNS → subdomains → vhosts → crawling → fingerprinting
```

---

## 💡 Key Insight

> [!TLDR]  
> Crawling is the **transition layer between passive recon and active attack surface discovery**.

---

If you want next-level improvement, I can combine everything you’ve built so far into a **single unified Web Recon Playbook**:

- DNS → Subdomains → CT logs → VHosts → Crawling → Fingerprinting → Exploitation surface mapping