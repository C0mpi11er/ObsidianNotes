# 🔍 Username Enumeration & OSINT

---

## 🕵️‍♂️ Overview of Username Enumeration Techniques

> [!ABSTRACT] 
> This section provides an overview and various methods to enumerate usernames from a target system.

### Footprinting via Web Research

Collecting information about the organization, its employees, and potential targets:

- **LinkedIn**: Search for employee profiles.
- **GitHub**: Look for public repositories.
- **Company Website**: Check "About Us" sections or any public forums/boards where users might post their work emails or usernames.

---

## 🛠️ Tools & Techniques

> [!NOTE]
> Utilize specific tools and methods to gather username information.

### Nmap Service Detection

```bash
nmap -sV <target-ip>
```

Identify services that may expose version information, potentially leaking internal usernames in comments or banners.

---

## 🚦 OSINT Gathering

Collecting publicly available data:

- **Email Scraping**: Use tools like `theHarvester` to scrape emails from various sources.
  
  ```bash
  theharvester -d example.com -b google -l 3
  ```

- **Social Media Mining**: Scrape usernames and contact details from social media platforms.

---

## 🔍 Enumeration via API Endpoints

> [!WARNING]
> Be cautious when interacting with APIs as it may trigger security mechanisms or alert administrators.

### Enumerating Users Through HTTP Headers

Check for user enumeration vulnerabilities in HTTP headers:

```bash
curl -s -I http://example.com/api/users/1 | grep 'HTTP'
```

---

## 📊 Analysis & Reporting

> [!SUCCESS]
> Compile all gathered information into a structured report.

### Summary Table of Gathered Usernames

| Source           | Username(s)   |
|------------------|---------------|
| LinkedIn         | john.doe      |
| GitHub           | johnd0e       |
| Company Website  | jane.smith    |

---

## ⚠️ Potential Risks & Mitigation Strategies

> [!WARNING]
> Be aware of the legal and ethical implications of OSINT activities.

### Risk Assessment

- **Legal Compliance**: Ensure all activities comply with relevant laws and regulations.
- **Ethical Considerations**: Respect privacy and security policies.

---

## 🔧 Custom Scripts for Username Enumeration

Create custom scripts to automate username enumeration tasks:

```bash
#!/bin/bash
for user in $(cat usernames.txt); do
  curl -s http://example.com/api/users/$user > /dev/null
done
```

> [!NOTE]
> Always test such scripts in a controlled environment.

---

## 🧠 Mental Model for Username Enumeration

```text
Footprint → Gather OSINT Data → Analyze HTTP Headers/API Endpoints → Automate with Scripts → Report Findings
```

This model emphasizes the systematic approach to gathering and utilizing username enumeration data effectively.