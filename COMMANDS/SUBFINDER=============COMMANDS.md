

---

#

> [!INFO] **Tool Overview**
> 
> Subfinder is a subdomain discovery tool that discovers subdomains for websites by using **passive** online sources. It is designed to be fast, modular, and easily integrated into automated reconnaissance pipelines.

### 🛠️ Basic Usage

> [!EXAMPLE] **Quick Commands**
> 
> Bash
> 
> ```
> # Basic scan for a single domain
> subfinder -d example.com
> 
> # Silent mode (Subdomains only, ideal for piping)
> subfinder -d example.com -silent
> 
> # Input from a file and save results
> subfinder -dL domains.txt -o results.txt
> ```

### ⚙️ Operational Flags

> [!ABSTRACT] **Input & Source Control**
> 
> - `-d`, `-domain`: Target domain to enumerate.
>     
> - `-dL`, `-list`: File containing a list of target domains.
>     
> - `-all`: Use all available sources (slower but more thorough).
>     
> - `-recursive`: Use sources that handle subdomains recursively.
>     
> - `-s`, `-sources`: Use specific sources only (e.g., `-s crtsh,github`).
>     
> - `-ls`: List all available passive sources.
>     

> [!TIP] **Optimization & Filtering**
> 
> - `-t`: Number of concurrent goroutines for resolving (Default: 10).
>     
> - `-rl`: Global rate-limit (requests per second).
>     
> - `-m`, `-match`: Match specific subdomains or patterns.
>     
> - `-f`, `-filter`: Filter out specific subdomains from results.
>     
> - `-timeout`: Seconds to wait before timing out (Default: 30).
>     

> [!IMPORTANT] **Output Formatting**
> 
> - `-oJ`: Write output in **JSONL** format for automated processing.
>     
> - `-oD`: Save results into a specific directory (Best for `-dL`).
>     
> - `-cs`: Include the source name for each found subdomain (Requires `-json`).
>     
> - `-oI`: Include host IP addresses in the output (Requires `-active`).
>     
> - `-nc`: Disable terminal colors for clean log files.
>     

### 🚀 Advanced Recon Workflow

> [!SUCCESS] **Automation Pipeline**
> 
> Subfinder is most powerful when combined with other tools to verify "live" targets:
> 
> Bash
> 
> ```
> subfinder -d example.com -silent | httpx -sc -td -title
> ```
> 
> _This pipes passive discovery into `httpx` to check status codes, technology detection, and page titles._

---

