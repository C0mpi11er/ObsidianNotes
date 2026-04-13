
## 💾 Nmap Output Formats

Nmap provides three primary formats for saving data, each serving a specific purpose:

|Format|Flag|Extension|Best Use Case|
|---|---|---|---|
|**Normal**|`-oN`|`.nmap`|Human readability; looks exactly like the terminal output.|
|**Grepable**|`-oG`|`.gnmap`|Automated parsing; ideal for using `grep`, `awk`, or `cut` to extract specific IPs or ports.|
|**XML**|`-oX`|`.xml`|Machine readability; used for importing into databases (like Metasploit) or converting to HTML reports.|
|**All Formats**|`-oA`|(All)|Generates all three files simultaneously using a base filename.|

Export to Sheets

---

## 📊 Generating Professional Reports

The XML output is particularly powerful because it can be transformed into a clean, visual HTML report using **Style Sheets (XSL)**. This is perfect for non-technical stakeholders or documentation.

### The Transformation Process

To convert your results, use the `xsltproc` utility:

Bash

```
# Convert XML to a readable HTML report
xsltproc target.xml -o target.html
```

Once converted, you can open the `.html` file in any web browser to see a structured table containing the host status, open ports, service versions, and script results.

---

## 💻 Command Reference (Copy & Paste)

### Saving Results

Bash

```
# Save in all formats with the prefix 'internal_scan'
sudo nmap <target> -oA internal_scan

# Save only in Grepable format for easy filtering
sudo nmap <target> -oG network_discovery.gnmap

# Scan all ports and save results
sudo nmap <target> -p- -oA full_port_scan
```

### Reporting & Filtering

Bash

```
# Convert XML to HTML
xsltproc scan_results.xml -o professional_report.html

# Quick Grep tip: List all 'open' ports from a gnmap file
grep "open" target.gnmap
```

> [!TIP] **Pro Workflow:** Always use `-oA`. It takes negligible disk space and saves you from having to re-run a scan if you suddenly need the XML for a tool or the Grepable format for a quick script later on.