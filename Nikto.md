Below is an ultra-comprehensive Nikto guide, tailored for BlackArch (or similar Arch-based distributions), covering everything from installation, basic and advanced usage, tuning options, output customization, integration, scheduling, and best practices. This guide includes every trick and technique you may need.

---

# **Nikto Comprehensive Guide (BlackArch Edition)**

## 🔥 Overview

**Nikto** is an open-source web server scanner written in Perl. It is designed to identify potentially dangerous files/programs, outdated versions, and misconfigurations on web servers. With a database of over 6,700 vulnerabilities and issues, Nikto is a staple in web application security assessments. It supports scanning multiple ports, SSL connections, proxy usage, and offers extensive tuning options.

**Key Capabilities:**

- **Vulnerability Detection:** Checks for dangerous files, outdated software, and configuration issues.
- **Multiple Port Support:** Scan one or more ports, or entire ranges.
- **SSL/TLS Support:** Scan both HTTP and HTTPS targets (with or without certificate verification).
- **Tuning Options:** Customize which tests to run or skip.
- **Proxy & Header Support:** Set custom HTTP headers or use a proxy for evasion.
- **Output Customization:** Generate results in various formats for integration with other tools.

---

## 🛠️ Installation on BlackArch

### Using Pacman

BlackArch provides Nikto in its repositories:

```bash
sudo pacman -Syu nikto
```

### Manual Installation (if needed)

1. **Clone the Repository:**
    
    ```bash
    git clone https://github.com/sullo/nikto.git
    ```
    
2. **Navigate to the Program Directory:**
    
    ```bash
    cd nikto/program
    ```
    
3. **Run Nikto:**
    
    ```bash
    perl nikto.pl -h http://target.com
    ```
    

_Ensure you have Perl installed on your system._

---

## 🚀 Basic Usage

### **Running a Basic Scan**

```bash
nikto.pl -h http://target.com
```

- **`-h`**: Specifies the target host. (Include scheme: `http://` or `https://`.)

This command initiates a basic scan against the target, checking for common vulnerabilities, misconfigurations, and known dangerous files.

---

## ⚙️ Detailed Options & Flags

### **Target & Port Options**

- **`-h <host>`**: Target host or URL.
- **`-p <port(s)>`**: Define a single port, a list (comma-separated), or a range.
    - Example: `-p 80,443` or `-p 1-1000`

### **Tuning Options**

Nikto lets you control which tests to include or exclude:

- **`-Tuning <option>`**: Select or skip tests. Each letter/digit corresponds to a test category.
    - For example, `-Tuning 123b` might run tests in categories 1, 2, 3, and b. (Consult the [Nikto tuning documentation](https://cirt.net/nikto2-docs) for the full list.)
- **Evasion Techniques:**
    - **`-evasion <technique>`**: Some versions allow splitting requests or altering headers. Options vary; for instance, `-evasion 1` might split requests.

### **Display & Verbosity**

- **`-Display V`**: Verbose output (shows each request/response detail).
- **`-Display E`**: Show errors only.
- **`-Display F`**: Show only failed tests.

### **Output Customization**

- **`-Format <format>`**: Specify output format. Options: `txt`, `csv`, `htm`, `xml`.
    - Example: `-Format xml`
- **`-o <file>`**: Write the scan results to a file.
    - Example: `-o results.xml`
- **`-Output <file>`**: Sometimes used interchangeably with `-o`.

### **SSL and HTTPS Options**

- **`-ssl`**: Force SSL usage (for HTTPS scanning).
- **`-no_ssl_verify`**: Skip certificate verification (useful with self-signed certs). (In some versions, this is `-k`.)
    - Example: `-ssl -no_ssl_verify`

### **Proxy and Custom Headers**

- **`-useproxy <proxy>`**: Route traffic through a proxy.
    - Example: `-useproxy http://127.0.0.1:8080`
- **`-C <cookie>`**: Supply cookies for session-based scanning.
    - Example: `-C "PHPSESSID=abcdef"`
- **`-useragent <agent>`**: Specify a custom User-Agent.
- **`-U <header>`**: Add a custom header (may require different syntax depending on version).

### **Debug and Timeout Options**

- **`-debug`**: Enable debugging output for troubleshooting.
- **`-timeout <seconds>`**: Set a request timeout (if available).

---

## 🚀 Advanced Scanning Techniques

### **1. Scanning Multiple Ports**

Scan non-standard or multiple ports:

```bash
nikto.pl -h http://target.com -p 80,8080,443
```

Or scan an entire range:

```bash
nikto.pl -h http://target.com -p 1-1000
```

### **2. Tuning and Excluding Tests**

Skip tests that might be noisy or irrelevant:

```bash
nikto.pl -h http://target.com -Tuning 1234
```

Or explicitly skip categories (refer to documentation for exact codes).

### **3. Proxy and Custom Header Usage**

To evade basic detection or target specific behaviors:

```bash
nikto.pl -h https://target.com -useproxy http://127.0.0.1:8080 -C "sessionid=xyz" -useragent "Mozilla/5.0 (compatible)"
```

### **4. Output Customization for Automation**

Save the results in a desired format for further analysis:

```bash
nikto.pl -h http://target.com -Format csv -o nikto_results.csv
```

### **5. Combining Options for Full Recon**

Combine enumeration, tuning, and output options to get a full report:

```bash
nikto.pl -h https://target.com -p 80,443 -Tuning 123b -ssl -no_ssl_verify -Display V -Format xml -o nikto_results.xml
```

---

## 🚀 Integration and Automation

### **Scripting and Scheduling**

- **Automated Scans:** Integrate Nikto into your penetration testing workflow by scheduling scans using cron.
    
    ```bash
    crontab -e
    # Run Nikto daily at 2 AM, saving output with the current date:
    0 2 * * * /usr/bin/perl /path/to/nikto.pl -h http://target.com -o /path/to/results_$(date +\%F).txt
    ```
    

### **Combining with Other Tools**

- **Chained Recon:** Use Nikto alongside Nmap, WPScan, or custom scripts to cross-validate vulnerabilities.
- **Reporting:** Parse Nikto's output (especially XML/CSV formats) with tools like Splunk or custom Python scripts for comprehensive reports.

---

## ⚠️ Best Practices & Tips

- **Legal Use:** Only scan web servers you have explicit permission to test.
- **Update Regularly:** Ensure you have the latest version of Nikto and its vulnerability database. Regularly pull updates from the official repository.
- **Interpreting Results:** Not every flagged item is exploitable. Use manual testing to verify vulnerabilities.
- **Performance:** Adjust thread counts and timeouts based on the target’s responsiveness. Aggressive settings may trigger IDS/IPS alerts.
- **Stealth Considerations:** While Nikto is a brute-force scanner by nature, using custom headers, proxy options, and careful timing can reduce detection.

---

## ⚠️ Troubleshooting Common Issues

- **Timeouts and Connection Errors:** Adjust the `-timeout` parameter or reduce thread counts if the target server is slow.
- **SSL Certificate Warnings:** Use `-ssl -no_ssl_verify` (or `-k`) to bypass certificate validation on self-signed certificates.
- **False Positives:** Cross-check vulnerabilities with additional tools and manual verification.

---

## ⚠️ Legal Disclaimer

💀 **Nikto should only be used on web servers you have explicit permission to scan. Unauthorized scanning is illegal and unethical.**

🚀 **Hack Responsibly!**

---

This guide should now serve as a detailed and comprehensive reference for using Nikto on BlackArch Linux, covering everything from installation and basic usage to advanced scanning techniques, integration, and troubleshooting. Let me know if you need any further details or additional sections!