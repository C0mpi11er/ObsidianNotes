```markdown
# 🗡️ Dictionary & Brute Force Attacks

---

> [!ABSTRACT] Overview of Dictionary and Brute Force Attacks
> This section covers various methods to perform dictionary attacks and brute force attacks on systems, including common tools used in these types of attacks.

## Common Tools for Dictionary Attacks

### [[Hydra]]

#### Usage Example

```bash
hydra -l user1 -P /usr/share/wordlists/rockyou.txt ssh://<IP>
```

> [!NOTE] 
> Hydra is a fast network login cracker that supports many different services.

---

## Common Tools for Brute Force Attacks

### [[John the Ripper]]

#### Usage Example

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt hashfile
```

> [!INFO]
> John the Ripper is a fast password cracker that supports many crypt(3) password hashing schemes for most Unix flavors and can be used anywhere passwords are stored in plain text or encrypted.

---

## Performing Brute Force Attacks on SSH

### Using [[Hydra]]

#### Command Example

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt ssh://10.10.10.10
```

> [!CHECK] 
> Ensure you have the correct permissions and that the target is not a production environment.

---

## Performing Dictionary Attacks on Web Applications

### Using [[Burp Suite]]

#### Command Example

```bash
burpsuite --load=proxy.yaml
```
- In Burp Suite, navigate to Intruder.
- Set up targets with HTTP requests for brute-forcing login pages.

> [!WARNING]
> Be cautious not to generate too much traffic as it may trigger WAF rules or cause a denial of service on the target.

---

## Performing Brute Force Attacks on RDP

### Using [[RDPBrute]]

#### Command Example

```bash
rdpbrute -H 10.10.10.10 -u admin -P /usr/share/wordlists/rockyou.txt
```

> [!DANGER] 
> This command can be destructive and should only be used in a controlled lab environment.

---

## Performing Brute Force Attacks on VNC

### Using [[vncbrute]]

#### Command Example

```bash
vncbrute -H 10.10.10.10 -P /usr/share/wordlists/rockyou.txt
```

> [!CAUTION] 
> Ensure that the target is a non-production environment before executing this command.

---

## Common Findings in Dictionary and Brute Force Attacks

| Finding | Impact |
|---|---|
| Weak Passwords | Easy to crack using dictionary attacks. |
| Unpatched Services | Vulnerable to brute force attacks due to known vulnerabilities. |
| No Account Lockout Policies | Allows unlimited attempts leading to successful breaches. |

---

# 🧠 Exam Mental Model

```text
Brute Force & Dictionary Attacks Rule of Thumb:
- Identify service (SSH, RDP, VNC, etc.)
- Use appropriate tool (Hydra, John the Ripper, Burp Suite)
- Craft payload using wordlist
- Execute attack carefully and monitor for success/failure.
```
> [!SUCCESS] 
> Always verify permissions before performing brute force or dictionary attacks. This rule ensures ethical hacking practices are maintained.

```