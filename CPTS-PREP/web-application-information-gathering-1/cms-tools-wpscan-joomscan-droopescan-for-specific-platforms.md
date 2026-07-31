```markdown
# 🎯 CMS Tools - wpscan, joomscan, droopescan for specific platforms

---

> [!ABSTRACT] Overview of CMS Tools
>
> This note covers the use of `wpscan`, `joomscan`, and `droopescan` tools specifically tailored to their target platforms: WordPress, Joomla, and Drupal respectively. These tools are used for scanning websites for security vulnerabilities, misconfigurations, and other issues.

---

## 🛠️ wpscan

### Description
> [!INFO] wpscan is a powerful command-line tool designed for auditing the security of WordPress installations.
>
> It can perform several tasks including fetching details about wp theme/plugin versions, enumerating users with their email addresses, finding out roles and capabilities, testing login credentials against the admin panel, extracting author IDs from posts/pages, listing user posts based on role/capability, and checking for outdated plugins/themes.

### Usage Examples
Anonymous Scan:
```bash
wpscan --url https://example.com
```
Authenticated Scan:
```bash
wpscan --url https://example.com --auth-username admin --auth-password 'password'
```

---

## 🛠️ joomscan

### Description
> [!INFO] joomscan is a command-line tool for auditing Joomla installations.
>
> It can perform various tasks like fetching details about Joomla version, checking if the installation is vulnerable to known exploits, enumerating users with their email addresses, finding out roles and capabilities, testing login credentials against the admin panel, extracting user IDs from articles, listing user articles based on role/capability.

### Usage Examples
Anonymous Scan:
```bash
joomscan --url https://example.com
```
Authenticated Scan:
```bash
joomscan --url https://example.com --auth-username admin --auth-password 'password'
```

---

## 🛠️ droopescan

### Description
> [!INFO] droopescan is a command-line tool for auditing Drupal installations.
>
> It can perform various tasks such as fetching details about Drupal version, checking if the installation is vulnerable to known exploits, enumerating users with their email addresses, finding out roles and capabilities, testing login credentials against the admin panel, extracting user IDs from nodes, listing user nodes based on role/capability.

### Usage Examples
Anonymous Scan:
```bash
droopescan --url https://example.com
```
Authenticated Scan:
```bash
droopescan --url https://example.com --auth-username admin --auth-password 'password'
```

---

## 📊 Comparison Table

| Tool Name | Target Platform | Key Features |
|---|---|---|
| wpscan | WordPress | Theme/Plugin version checks, user enumeration, role/capability listing, login credential testing. |
| joomscan | Joomla | Version fetching, exploit checks, user/email enumeration, role/capability listing, login credential testing. |
| droopescan | Drupal | Version fetching, exploit checks, user/email enumeration, role/capability listing, login credential testing. |

---

## 📝 Notes
> [!NOTE]
> It is important to use these tools responsibly and ethically. Ensure you have explicit permission before conducting security audits on any websites.

---

# 🔍 Detailed Walkthroughs

### wpscan Walkthrough
```bash
wpscan --url https://example.com --enumerate u  # Enumerate users
```
> [!SUCCESS]
> Successfully enumerated WordPress user details.
>
> ```text
> User ID: 1
> Username: admin
> Email: admin@example.com
> Roles: Administrator, Editor
> ```

### joomscan Walkthrough
```bash
joomscan --url https://example.com --test-auth --auth-username admin --auth-password 'password' # Test authentication
```
> [!SUCCESS]
> Authentication test successful for Joomla installation.

### droopescan Walkthrough
```bash
droopescan --url https://example.com --list-users  # List users
```
> [!WARNING] 
> Ensure to use these commands only on authorized systems due to potential legal implications of unauthorized access or misuse.
>
> ```text
> User ID: 1
> Username: admin
> Email: admin@example.com
> Roles: Administrator, Editor
> ```

---

# 📄 Additional Resources

- [[wpscan Documentation]] - Comprehensive guide and API documentation for wpscan.
- [[joomscan Documentation]] - Detailed usage and features of joomscan.
- [[droopescan Documentation]] - In-depth information about droopescan functionalities.

```