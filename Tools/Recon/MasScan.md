

---

# 🛰️ Masscan Cheat Sheet (Ultra-Fast Port Scanner)

## 📌 Overview

- **Masscan** = ultra-fast port scanner (can scan the entire internet in minutes)
    
- Works like a **stateless SYN scanner**
    
- Much faster than Nmap but less detailed
    
- Best for:
    
    - Wide recon (huge IP ranges)
        
    - Initial attack surface discovery
        

---

## ⚙️ Basic Syntax

```bash
masscan <target> -p<ports> [options]
```

---

## 🚀 Quick Examples

### 🔹 Scan Single Target

```bash
masscan 192.168.1.1 -p80
```

### 🔹 Scan Multiple Ports

```bash
masscan 192.168.1.1 -p22,80,443
```

### 🔹 Scan All Ports

```bash
masscan 192.168.1.1 -p1-65535
```

---

## 🌍 Scan Large Networks

### 🔹 Entire Subnet

```bash
masscan 192.168.1.0/24 -p80
```

### 🔹 Huge Range (Internet-scale)

```bash
masscan 0.0.0.0/0 -p80 --rate 1000000
```

⚠️ Be careful: this is **VERY aggressive** and can get you blocked.

---

## ⚡ Performance Tuning

### 🔹 Set Scan Rate (VERY IMPORTANT)

```bash
--rate <packets_per_second>
```

Example:

```bash
masscan 192.168.1.0/24 -p80 --rate 1000
```

- Low rate = stealthier
    
- High rate = faster but noisy
    

---

## 🧠 Interface & Network Settings

### 🔹 Specify Interface

```bash
-e eth0
```

### 🔹 Set Source IP

```bash
--source-ip 192.168.1.100
```

### 🔹 Set Source Port

```bash
--source-port 40000
```

---

## 📤 Output Options

### 🔹 Normal Output

```bash
-oL output.txt
```

### 🔹 JSON Output

```bash
-oJ output.json
```

### 🔹 Grepable Output

```bash
-oG output.grep
```

---

## 🔍 Banner Grabbing (Limited)

```bash
--banners
```

Example:

```bash
masscan 192.168.1.1 -p80 --banners
```

⚠️ Not as reliable as Nmap service detection.

---

## ⏱️ Timing & Reliability

### 🔹 Wait for Responses

```bash
--wait 5
```

### 🔹 Retries

```bash
--retries 3
```

---

## 🎯 Target Input

### 🔹 From File

```bash
-iL targets.txt
```

---

## 🚫 Exclude Targets

```bash
--exclude 192.168.1.5
```

```bash
--excludefile exclude.txt
```

---

## 🔄 Resume Scan

```bash
--resume paused.conf
```

---

## 🔐 Useful Flags Summary

|Flag|Purpose|
|---|---|
|`-p`|Ports|
|`--rate`|Speed control|
|`-oL/-oJ/-oG`|Output formats|
|`-iL`|Input file|
|`--exclude`|Skip targets|
|`--banners`|Grab banners|
|`--wait`|Wait for responses|
|`-e`|Interface|

---

## 🧪 Real-World Workflow

### 🔹 Step 1: Fast Discovery

```bash
masscan 10.0.0.0/16 -p80,443,22 --rate 5000 -oG masscan.grep
```

### 🔹 Step 2: Extract Live Hosts

```bash
grep "open" masscan.grep | awk '{print $2}' > live_hosts.txt
```

### 🔹 Step 3: Deep Scan with Nmap

```bash
nmap -iL live_hosts.txt -sV -sC
```

---

## ⚠️ Key Notes (VERY IMPORTANT)

- Masscan uses its **own TCP/IP stack**
    
- Can **bypass OS firewall rules**
    
- May require:
    

```bash
sudo masscan ...
```

- Can conflict with your system networking → use:
    

```bash
--source-port
```

---

## 🧠 Pro Tips

- Always start with a **safe rate** (e.g., 100–1000)
    
- Use Masscan for:
    
    - Speed ✅
        
- Use Nmap for:
    
    - Detail ✅
        
- Combine both for **efficient recon pipelines**
    

---

## 🧩 Mental Model (Simple)

Think of Masscan as:

> “Spray packets everywhere → see who responds → analyze later”

---

If you want, I can give you:

- 🔥 **Masscan + Nmap automation script**
    
- ⚔️ **Red team recon workflow**
    
- 🧠 **How Masscan actually works internally (packet crafting, SYN scanning)**