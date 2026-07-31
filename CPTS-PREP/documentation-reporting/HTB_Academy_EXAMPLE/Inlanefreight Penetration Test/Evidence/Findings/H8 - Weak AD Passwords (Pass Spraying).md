# 🔐 Weak AD Passwords (Pass Spraying)

> [!ABSTRACT] Summary of Pass Spraying Technique
>
> The pass spraying technique is used to exploit weak and commonly used passwords in Active Directory environments, aiming to gain unauthorized access to user accounts.

---

## 🌍 Understanding the Scenario

> [!INFO] What is Pass Spraying?
>
> Pass spraying involves attempting a large number of common password combinations against many different user accounts. The goal is to find users who have set their password to one of these commonly used passwords, thereby gaining access without using any credentials that require specific knowledge about each individual.

---

## 🖥️ Initial Steps

### Reconnaissance

> [!CHECK] Checking for AD Users
>
> Before attempting pass spraying, it's crucial to gather a list of potential user accounts. Tools like [[CrackMapExec]] can help enumerate users and extract necessary information.
```bash
crackmapexec smb <IP> -u usernames.txt
```

### List Preparation

Create or obtain a wordlist of commonly used passwords.

---

## 🔑 Executing Pass Spraying

### Using CrackMapExec

#### Local Accounts

Anonymous login attempts can be made if the service is configured to allow it.

```bash
crackmapexec smb <IP> -u usernames.txt -p password_list.txt --local-auth
```

#### Domain Accounts

Authenticate with a list of domain users and passwords. 

```bash
crackmapexec smb <IP> -d example.com -u usernames.txt -p password_list.txt
```

---

## 📜 Important Notes & Considerations

### Potential Obstacles

> [!WARNING] Be Prepared for Lockouts
>
> Mass account lockouts can occur if a large number of failed login attempts are made. Administrators may have set up policies to protect against such attacks.

### Mitigation Strategies

Implementing multi-factor authentication (MFA) and educating users about strong password practices can mitigate the risk of pass spraying attacks.

---

## 🔍 Verification & Validation

### Confirming Success

> [!SUCCESS] Verifying a Successful Login
>
> If any login attempt is successful, you will have gained access to that user's account. The next steps involve assessing privileges and exploring lateral movement opportunities.
```bash
crackmapexec smb <IP> -d example.com -u valid_user -p valid_password
```

### Documenting Failures

If pass spraying fails, documenting the reasons (e.g., complex password policies) is critical for future assessments.

---

## 🧭 Next Steps After Pass Spraying

### Lateral Movement and Privilege Escalation

Once a foothold has been established through weak passwords, focus on moving laterally to other machines or escalating privileges within the AD environment using techniques like [[Pass-the-Hash]] or [[Kerberos Ticket Granting Tickets (TGT)]].

---

## 📝 Additional Resources & References

### Tools and Techniques
- [[CrackMapExec]]
- [[Nmap]]

### Documentation Links
> [!QUOTE] 
> Refer to the official documentation for a comprehensive understanding of pass spraying techniques: https://book.hacktricks.xyz/windows-hardening/logging-and-auditing/passtrough-and-pass-spraying

---

# 📄 Conclusion

Pass spraying is an effective method to identify weak password usage in AD environments, allowing attackers to gain unauthorized access. Understanding how and when to employ this technique is crucial for both offensive testing and defensive strategies.

> [!NOTE] 
> Always ensure you have proper authorization before conducting any penetration tests or security assessments on target systems.