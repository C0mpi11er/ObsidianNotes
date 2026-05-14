# 🤖 robots.txt Cheat Sheet

---

> [!ABSTRACT] 🧠 What is robots.txt?
> 
> `robots.txt` is a **public file at the root of a website** that tells crawlers (bots) what they are allowed or not allowed to access.
> 
> 📍 Location:
> 
> ```text
> https://target.com/robots.txt
> ```
> 
> It follows the **Robots Exclusion Standard**.

---

## 📌 Basic Structure

> [!INFO]
> 
> A typical `robots.txt` contains:
> 
> - User-agent (which bot the rule applies to)
>     
> - Disallow (blocked paths)
>     
> - Allow (exceptions)
>     
> - Sitemap (index of site URLs)
>     
> - Crawl-delay (rate limiting)
>     

---

## ⚙️ Example File

> [!EXAMPLE]

```txt
User-agent: *
Disallow: /admin/
Disallow: /private/
Allow: /public/

User-agent: Googlebot
Crawl-delay: 10

Sitemap: https://target.com/sitemap.xml
```

---

## 📌 Key Directives

> [!INFO]

|Directive|Meaning|
|---|---|
|`User-agent`|Defines which bot rules apply to|
|`Disallow`|Blocks access to a path|
|`Allow`|Explicitly permits access|
|`Crawl-delay`|Adds delay between requests|
|`Sitemap`|Points to XML URL index|

---

## 🔍 How to Check robots.txt

> [!EXAMPLE]

```bash
curl https://target.com/robots.txt
```

or open directly in browser:

```text
https://target.com/robots.txt
```

---

## 🕵️ Recon Value (VERY IMPORTANT)

> [!SUCCESS]

robots.txt often reveals:

- Hidden admin panels
    
- Backup directories
    
- Staging environments
    
- Sensitive endpoints
    
- API paths
    
- Disallowed but real directories
    

---

## 📂 Common Interesting Paths

> [!ATTENTION]

```text
/admin/
/administrator/
/backup/
/test/
/dev/
/private/
/internal/
/config/
/old/
/uploads/
/api/
```

---

## ⚠️ Important Reality Check

> [!WARNING]
> 
> `robots.txt` is NOT a security control.
> 
> It is:
> 
> - Publicly accessible
>     
> - Ignored by malicious actors
>     
> - Only respected by well-behaved bots (Googlebot, Bingbot, etc.)
>     

---

## 🧪 Recon Workflow

> [!CHECK]

Step 1: Fetch file

```bash
curl https://target.com/robots.txt
```

Step 2: Extract paths

```bash
cat robots.txt | grep Disallow
```

Step 3: Test discovered endpoints

```bash
curl https://target.com/admin/
```

Step 4: Feed into crawler / fuzzing tools

---

## 🚨 Common Mistake

> [!CAUTION]
> 
> Thinking:
> 
> “If it’s disallowed in robots.txt, I cannot access it.”
> 
> ❌ Wrong.
> 
> It only means:
> 
> “Search engines should not index it.”

---

## 💣 Why Attackers Love It

> [!SUCCESS]

Because it often leaks:

- `/admin` panels
    
- `/backup.zip`
    
- `/staging/`
    
- `/api/internal/`
    
- forgotten dev endpoints
    

---

## 🧠 TLDR

> [!TLDR]
> 
> `robots.txt` = **public roadmap of what site owners want hidden from search engines**
> 
> But in recon:
> 
> 👉 it’s a **map of hidden attack surfaces**

---