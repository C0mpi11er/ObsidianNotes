# 📂 Directory Listing Enabled

> [!ABSTRACT] Overview of Directory Listing Enabled Vulnerability
>
> A directory listing is a default feature in many web servers that lists all files and subdirectories within a specific folder when accessed directly. This can expose sensitive information, such as file paths, server configurations, or even codebase details.

---

## 📄 What is Directory Listing?

> [!INFO] Definition
>
> Directory listing is an HTTP response to a request for a directory that does not contain an index.html file (or other specified default document). The web server responds with a list of all the files in the requested directory, potentially revealing sensitive information.

---

## 🛠️ Common Exploitation Scenarios

### Unauthorized Access

> [!WARNING] Security Risk
>
> If users can view a directory listing on a server that contains sensitive files (like `config.php` or `.htaccess`), they might be able to gather enough information to perform further attacks, such as SQL injection or file inclusion.

---

## ⚙️ Enabling Directory Listing

### Apache Configuration Example

> [!EXAMPLE] Configuring Directory Indexes in Apache
>
```apache
<Directory "/var/www/html">
    Options +Indexes
</Directory>
```

---

## 🛡️ Disabling Directory Listing

### Web Server Settings

#### Apache (Disable)

> [!SUCCESS] Disable directory listing successfully.
>
```apache
<Directory "/var/www/html">
    Options -Indexes
</Directory>
```
Restart the web server to apply changes:
```bash
sudo systemctl restart apache2
```

---

## 📃 Impact of Directory Listing

| Scenario | Impact |
|---|---|
| Unauthorized Information Exposure | Data theft, code injection vulnerabilities. |
| Misconfigured Access Controls | Easier for attackers to locate sensitive files or directories. |

---

## 🔍 Discovery Methods

### Scanning with Nmap

> [!SUCCESS] Successful scan results.
>
```bash
nmap -p 80 --script=http-server-header <IP_ADDRESS>
```

Check directory listing:
```bash
curl http://<IP_ADDRESS>/directory/
```
If a list of files appears, directory listing is enabled.

---

## 🛡️ Mitigation Strategies

### Server Configuration Updates

#### Apache

Disable directory listing by setting `Options -Indexes` in the configuration file and restart the server to ensure changes are applied. This prevents unauthorized users from seeing directory listings.

> [!NOTE] Important
>
> Always test configurations on a staging environment before applying them to production servers.

---

## 🧠 Mental Model for Directory Listing

Whenever you encounter an unexpected listing of files in a web application, immediately consider the potential impact and steps to mitigate this exposure. Ensure proper configuration settings are applied to avoid accidental data leakage.

```text
Enable -> Check Security Impact -> Disable if Unsafe
```

> [!SUCCESS] Best Practice Summary
>
> Properly configure your server to disable directory listing by default unless explicitly required for a specific use case, and regularly audit configurations to ensure compliance with security policies.

---

## 📌 High-Value Files to Protect

Ensure that sensitive files such as `.htaccess`, `wp-config.php`, or `database.yml` are not listed in public directories. Regularly review directory permissions and server configurations to prevent accidental exposure.

```text
.htaccess, wp-config.php, database.yml, config.php, .env
```

---

## ⚠️ Common Findings

| Finding | Impact |
|---|---|
| Directory Listing Enabled | Information leakage, potential for further exploitation. |

---

## 📝 Additional Considerations

### Web Application Firewalls (WAF)

Integrate a WAF to monitor and control web traffic based on predefined security rules. This can help prevent unauthorized directory listings by blocking suspicious requests.

> [!HELP] Refer to Documentation
>
> Review WAF documentation for specific instructions on how to configure rulesets that block directory listing requests.

---

## 🧬 Next Steps

Ensure your server configurations adhere to best practices and regularly audit these settings for compliance. Implement additional security measures such as firewalls or intrusion detection systems to further protect against unauthorized access through directory listings.