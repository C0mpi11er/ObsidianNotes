---
# 🕸️ DNS Tools - dig, dnsenum, amass, puredns for subdomain discovery

---

## 🔍 Introduction to DNS Subdomain Discovery

> [!ABSTRACT] Overview of DNS Subdomain Discovery
>
> This note covers the use of various tools such as `dig`, `dnsenum`, `amass`, and `puredns` to discover subdomains in a target domain. These tools are essential for reconnaissance during cybersecurity assessments.

---

## 🔍 Dig Command Usage

> [!INFO] What is dig?
>
> The `dig` command-line tool is used to query DNS servers for information about domain names or IP addresses and display the answers returned by the name server.

### Basic Syntax
```bash
dig +nocmd +noall +answer <domain>.com any
```

---

## 🔍 Dnsenum Command Usage

> [!INFO] What is dnsenum?
>
> `dnsenum` performs DNS enumeration on a target domain to discover subdomains, IP addresses, MX records, and more.

### Basic Syntax
```bash
dnsenum --plain <domain>.com
```

---

## 🔍 Amass Command Usage

> [!INFO] What is amass?
>
> `amass` (Automated Mapper & Asset Tracker) is a fast and robust subdomain enumeration tool that leverages public DNS records.

### Basic Syntax
```bash
amass enum -d <domain>.com
```

---

## 🔍 PureDNS Command Usage

> [!INFO] What is puredns?
>
> `puredns` is another efficient subdomain enumeration tool designed for speed and accuracy, using Go programming language.

### Basic Syntax
```bash
puredns enum -d <domain>.com wordlist.txt -o output.txt
```

---

## 🔍 DNS Enumeration Workflow

1. **Identify target domain**.
   ```text
   Target: example.com
   ```
2. **Use `dig` for basic query**.
   ```bash
   dig +nocmd +noall +answer example.com any
   ```
3. **Execute a comprehensive enumeration with `dnsenum`**.
   ```bash
   dnsenum --plain example.com
   ```
4. **Run subdomain discovery using `amass`**.
   ```bash
   amass enum -d example.com
   ```
5. **Perform extensive subdomain probing with `puredns`**.
   ```bash
   puredns enum -d example.com wordlist.txt -o output.txt
   ```

---

## 📑 DNS Enumeration Results

> [!NOTE] Common Findings
>
> The results from the above tools will vary depending on the target domain. Some common findings include:
- Subdomains like `subdomain.example.com`
- Additional IP addresses associated with the domain

### Example Output
```text
example.com
api.example.com (192.0.2.1)
mail.example.com (192.0.2.2)
```

---

## 🧠 Mental Model for DNS Enumeration

> [!SUCCESS] DNS Discovery Rule of Thumb
>
> **Whenever you are working on a new target domain, immediately think:**
>
> ```text
> dig → dnsenum → amass → puredns
> ```
>
> These steps help in gathering comprehensive information about the subdomains and associated IP addresses for further analysis.

---

## 🔒 Security Considerations

> [!WARNING] Ethical Use of DNS Enumeration Tools
>
> Ensure that you have permission to perform reconnaissance on the target domain. Unauthorized scanning can be considered illegal or unethical.
  
---

## 🧑‍💻 Hands-On Exercises

1. **Install necessary tools**:
   ```bash
   sudo apt-get install dnsutils
   go get -u github.com/OWASP/Amass/v3/...
   go get -u github.com/d3mondev/puredns
   ```

2. **Run a full enumeration on your lab environment**.
   ```text
   Target: testdomain.com
   ```

---

## 📄 References

- [dig](https://linux.die.net/man/1/dig)
- [dnsenum](http://www.kalitesting.com/tools/dnsenum/)
- [Amass](https://github.com/OWASP/Amass)
- [PureDNS](https://github.com/d3mondev/puredns)