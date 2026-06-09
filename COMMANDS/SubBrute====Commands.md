Here is the command cheat sheet for SubBrute, organized using call-out blocks for quick reference and scanning.

## 🚀 SubBrute Command Cheat Sheet

### 1. Basic Enumeration

> ### 🔹 Single Domain Scan
> 
> Run a standard scan against a single target domain using the default wordlist:
> 
> Bash
> 
> ```
> ./subbrute.py google.com
> ```

> ### 🔹 Multiple Target Domains
> 
> Pass multiple domains separated by spaces to scan them sequentially:
> 
> Bash
> 
> ```
> ./subbrute.py google.com gmail.com blogger.com
> ```

> ### 🔹 Target List Input (`-t`)
> 
> Scan a large number of domains provided in a newline-delimited text file:
> 
> Bash
> 
> ```
> ./subbrute.py -t list.txt
> ```

### 2. Output & Debugging

> ### 💾 Save Results to File (`-o`)
> 
> Save the enumerated subdomain list directly to a text file:
> 
> Bash
> 
> ```
> ./subbrute.py google.com -o google.names
> ```

> ### 🔍 Show Associated IPs (`-a`)
> 
> Display all IP addresses mapping to each discovered subdomain:
> 
> Bash
> 
> ```
> ./subbrute.py -a google.com
> ```

> ### ⚙️ Verbose Debug Mode (`-v`)
> 
> Enable full debug output to troubleshoot tool performance or query status:
> 
> Bash
> 
> ```
> ./subbrute.py -v google.com
> ```

### 3. Advanced DNS Spidering (`-s`)

SubBrute recursively crawls DNS records automatically. You can chain scans together by feeding a results file back into the tool using the `-s` flag to look up specific record types (`--type`).

> ### 📋 Enumerate TXT Records
> 
> Check the discovered subdomains for TXT data (useful for finding SPF records or domain verifications):
> 
> Bash
> 
> ```
> ./subbrute.py -s google.names google.com --type TXT
> ```

> ### 🔗 Enumerate CNAME Records
> 
> Identify canonical name records to map out third-party aliases and configurations:
> 
> Bash
> 
> ```
> ./subbrute.py -s google.names google.com --type CNAME
> ```

> ### 🌐 Enumerate Other Record Types
> 
> Enumerate other standard records like IPv6 addresses (`AAAA`), mail servers (`MX`), or Start of Authority (`SOA`):
> 
> Bash
> 
> ```
> ./subbrute.py -s google.names google.com --type MX
> ```

### 4. Advanced Subdomain Chaining

> ### 🌀 Deep Nested Subdomain Scanning
> 
> Subdomains often contain deeper nested subdomains (e.g., `_tcp.gmail.com`). You can pipe standard output into a file and treat it as your new target list to dig deeper:
> 
> Bash
> 
> ```
> ./subbrute.py gmail.com > gmail.out
> ./subbrute.py -t gmail.out
> ```

### 5. Alternative Platforms & Environments

> ### 🪟 Windows Execution
> 
> Windows users can run the pre-compiled binary without manual setup by opening a command prompt and navigating to the `windows` directory:
> 
> DOS
> 
> ```
> cd windows
> subbrute.exe google.com
> ```

> ### 🐍 Python Library Usage
> 
> SubBrute can be imported directly into Python scripts to automate data collection within larger workflows:
> 
> Python
> 
> ```
> import subbrute
> 
> for d in subbrute.run("google.com"):
>     print d
> ```



>[!NOTE]
>all this is bullshit
>step 1 add ip to resolver.txt 
>step 2 brute fore domain with name list and resolver the dig axfr all sub domains
>e.g ./subbrute.py domain.htb -s subdomain.list -r resolver.txt
>each subdomain found should be dig axfr 