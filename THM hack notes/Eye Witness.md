Sure! Here's the **cheat sheet for the first tool, EyeWitness**, in a structured and comprehensive format.

---

### 🛠️ **EyeWitness Cheat Sheet**

**Category:** Discovery / Reconnaissance  
**Purpose:** EyeWitness is used to take screenshots of websites, collect server headers, and identify default credentials.

---

#### 📦 Installation

```bash
# Clone and install dependencies
git clone https://github.com/FortyNorthSecurity/EyeWitness.git
cd EyeWitness/Python
sudo ./setup.sh
```

---

#### 🚀 Basic Usage

```bash
# From a file with IPs or URLs
./EyeWitness.py --web -f targets.txt -d output_folder

# From Nmap XML output
./EyeWitness.py --web --nmap nmap_output.xml -d output_folder

# Use RDP module
./EyeWitness.py --rdp -f rdp_targets.txt -d output_folder

# Use VNC module
./EyeWitness.py --vnc -f vnc_targets.txt -d output_folder
```

---

#### 🔍 Key Options

|Option|Description|
|---|---|
|`--web`|Specify targets are web servers|
|`--rdp`|Screenshots of RDP services|
|`--vnc`|Screenshots of VNC services|
|`--nmap`|Parse Nmap XML for targets|
|`-f`|Input file (IP/URLs or XML)|
|`-d`|Output directory|
|`--headless`|Run Chrome in headless mode|
|`--user-agent`|Set custom user-agent|
|`--timeout`|Set timeout for page loading|

---

#### 📁 Output

- **HTML Report**: Easily navigable screenshot report.
    
- **JSON/CSV Exports**: Optional structured output.
    
- **Screenshots Folder**: All grabbed images.
    

---

#### 🧠 Tips

- Use Nmap scan with `-oX` to create XML for EyeWitness.
    
- Add `--headless` on remote servers or VPS without a GUI.
    
- Use in conjunction with **Masscan** or **Nmap** for maximum results.
    

---

Let me know when you're ready and I'll continue with the next tool: **HTTPScreenshot**.