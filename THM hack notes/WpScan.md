Below is a comprehensive WPScan note, tailored with extensive details, advanced options, and best practices for use on BlackArch (or any other environment):

---

# **WPScan Comprehensive Guide**

## 🔥 Overview

**WPScan** is a specialized WordPress security scanner written in Ruby. It’s designed to enumerate vulnerabilities, plugins, themes, and user accounts on WordPress sites. By comparing discovered information against a vulnerability database, WPScan helps penetration testers and security professionals assess the security posture of a WordPress installation.

**Key Features:**

- **Vulnerability Enumeration:** Check for known security issues in WordPress core, plugins, and themes.
- **User Enumeration:** Discover valid usernames that can be targeted for brute-force attacks.
- **Plugin & Theme Enumeration:** Identify installed plugins and themes, and cross-reference them with known vulnerabilities.
- **API Integration:** Use the WPScan Vulnerability Database (WPSVD) via an API token to get up-to-date vulnerability data.
- **Customizable Scans:** Various flags let you fine-tune the scan for stealth, speed, and output formats.

---

## 🛠️ Installation on BlackArch

Since BlackArch is based on Arch Linux, you can install WPScan directly from the repositories:

```bash
sudo pacman -Syu wpscan
```

_Alternatively, if you prefer installing via Ruby gems (or if the package is not available):_

1. Ensure you have Ruby and Bundler installed:
    
    ```bash
    sudo pacman -S ruby bundler
    ```
    
2. Install WPScan using gem:
    
    ```bash
    gem install wpscan
    ```
    
3. Make sure your Ruby bin directory is in your `PATH`.

---

## 🚀 Basic Usage

### **1. Basic Scan**

To perform a basic scan on a target WordPress site:

```bash
wpscan --url http://example.com
```

- **`--url`**: Specifies the target URL.
- WPScan will automatically attempt to detect the WordPress version, enumerate users (if possible), and list vulnerable plugins/themes.

---

## 🏹 Enumeration Options

WPScan provides several enumeration options to uncover details about the target:

### **User Enumeration**

```bash
wpscan --url http://example.com --enumerate u
```

- **`--enumerate u`**: Enumerates usernames from the WordPress site.

### **Plugin Enumeration**

```bash
wpscan --url http://example.com --enumerate p
```

- **`--enumerate p`**: Enumerates plugins and checks for known vulnerabilities.

### **Theme Enumeration**

```bash
wpscan --url http://example.com --enumerate t
```

- **`--enumerate t`**: Enumerates themes.

### **Combined Enumeration**

You can combine options by using a comma-separated list:

```bash
wpscan --url http://example.com --enumerate u,p,t
```

- This command enumerates users, plugins, and themes simultaneously.

---

## 🔐 Vulnerability Scanning

### **Using the WPScan Vulnerability Database**

WPScan can cross-reference discovered plugins, themes, and WordPress versions against its vulnerability database. For more accurate and up-to-date results, you can use an API token:

```bash
wpscan --url http://example.com --api-token YOUR_API_TOKEN
```

- **`--api-token`**: Provides your WPScan API key, which you can obtain from the [WPScan website](https://wpscan.com/).

---

## 🚀 Advanced Options & Techniques

### **Randomize User Agent**

To avoid detection and mimic different browsers:

```bash
wpscan --url http://example.com --random-agent
```

- **`--random-agent`**: Uses a random user agent string for each request.

### **Timeouts and Connection Options**

Control the behavior of HTTP requests:

- **`--request-timeout`**: Set a timeout for HTTP requests (in seconds).
- **`--follow-redirection`**: Follow HTTP redirects automatically.
- Example:
    
    ```bash
    wpscan --url http://example.com --request-timeout 10 --follow-redirection
    ```
    

### **Output Customization**

Save the scan output in various formats for later analysis:

- **`--output`**: Specify a file to write output.
- **`--format`**: Choose an output format (cli, json, or plain).
- Example:
    
    ```bash
    wpscan --url http://example.com --enumerate p --output results.json --format json
    ```
    

### **Proxy Support**

If you need to route your traffic through a proxy (for anonymization or bypassing filters):

```bash
wpscan --url http://example.com --proxy http://127.0.0.1:8080
```

- **`--proxy`**: Specifies the proxy address.

### **Verbose and Debug Modes**

For more detailed output:

- **`-v` or `--verbose`**: Increases verbosity.
- **`--debug`**: Outputs debugging information.
- Example:
    
    ```bash
    wpscan --url http://example.com --enumerate u --verbose --debug
    ```
    

### **Custom Wordlists**

For better user enumeration or plugin/theme guessing, you can use custom wordlists:

```bash
wpscan --url http://example.com --enumerate u --wordlist /path/to/custom_userlist.txt
```

- **`--wordlist`**: Specify a wordlist file.

---

## 🚀 Example Comprehensive Command

Combine multiple features for an in-depth scan:

```bash
wpscan --url https://fctirs.gov.ng --enumerate u,vp,vt,cb,dbe --plugins-detection aggressive --detection-mode aggressive --random-user-agent --follow-redirection --api-token YOUR_API_TOKEN --output wpscan_report.txt --format cli-no-color --throttle 150
```

- ### Technical Breakdown of Enhancements

#### 1. Corrected and Expanded Enumeration flags (`--enumerate u,vp,vt,cb,dbe`)

- **`u`**: Usernames (via author archives and REST API loops).
    
- **`vp`**: **Vulnerable Plugins exclusively**. This drastically cuts down on scan noise compared to searching for thousands of generic plugins, saving your daily API token request allocation for actual exposures.
    
- **`vt`**: **Vulnerable Themes exclusively**.
    
- **`cb`**: Config Backups. Automatically checks for exposed developer left-overs (like `wp-config.php.bak`, `wp-config.old`, or `.env`).
    
- **`dbe`**: Database Exports. Searches for common raw database dumps (like `dump.sql`, `backup.sql`, `wordpress.sql`) sitting in public root storage folders.
    

#### 2. Maximizing Finding Precision

- **`--plugins-detection aggressive`**: Forces `wpscan` to explicitly fuzz the plugin directories via a dictionary loop of known assets, rather than blindly relying on reading the landing page HTML content. This is essential for discovering hidden backend plugins (like your target's legacy `revslider` or rogue `/gacor/` injection artifacts).
    
- **`--detection-mode aggressive`**: Ensures deep-dive analysis across all structural file checks.
    

#### 3. Operational Performance & Evasion Tweaks

- **`--random-user-agent`**: The correct modern syntax parameter (replaces your legacy `--random-agent` flag) to cycle web browser request headers.
    
- **`--format cli-no-color`**: Changes the output style from raw standard terminal markup (`cli`) to a clean, non-colored layout format. This makes the resulting output text file significantly cleaner when importing the data directly into your Obsidian technical vault or writing a penetration test report.
    
- **`--throttle 150`**: Adds a micro-delay millisecond spacer between HTTP requests. This helps prevent crashing weak target application servers and reduces the likelihood of triggering edge firewall blocks during intense folder fuzzing sweeps.

---

## ⚠️ Legal Disclaimer

💀 **WPScan should only be used on systems for which you have explicit permission to test. Unauthorized scanning is illegal and unethical.**

🚀 **Hack Responsibly!**

---

This guide covers the full range of WPScan features and techniques on BlackArch, from installation and basic scans to advanced enumeration and customization. Let me know if you need further details or additional sections!