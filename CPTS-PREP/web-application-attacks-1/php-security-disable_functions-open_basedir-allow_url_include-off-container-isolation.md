# 🛠️ PHP Security - disable_functions, open_basedir, allow_url_include=Off, Container isolation

---

## 🔍 Overview of PHP Configuration Settings

> [!ABSTRACT] Summary
> This note covers important PHP security settings such as `disable_functions`, `open_basedir`, and `allow_url_include`. It also discusses the use of container isolation techniques to enhance application security.

### Disable Functions

PHP's `disable_functions` setting allows you to specify a list of functions that should be disabled within your PHP environment. This is useful for reducing attack vectors and preventing malicious code execution by disabling dangerous or potentially harmful functions such as `system()`, `exec()`, `shell_exec()`, etc.

> [!NOTE] Example
> ```ini
[PHP]
disable_functions = "exec,passthru,shell_exec"
```

### Open Basedir

The `open_basedir` directive restricts PHP to the specified directory tree. This prevents scripts from accessing files outside of this designated area, enhancing security by limiting file inclusion risks.

> [!NOTE] Example
> ```ini
[PHP]
open_basedir = "/var/www/html:/etc/php/"
```

### Allow URL Include

Disabling `allow_url_include` can prevent the inclusion of remote URLs via PHP's include/require functions. This helps to mitigate security threats such as Remote File Inclusion (RFI) attacks.

> [!WARNING] Security Risk
> Enabling `allow_url_include` may expose your application to RFI attacks, allowing attackers to potentially execute malicious code by including a URL that points to an external script or file.

### Container Isolation

Using container isolation techniques like Docker can further enhance the security of PHP applications. By running each application in its own isolated environment, you limit potential damage if one application is compromised.

> [!INFO] Example
> ```bash
docker run --name my-php-app -v "$PWD":/var/www/html -p 80:80 php:7.4-apache
```

---

## 🚀 Configuring PHP Security Settings

### Step-by-Step Configuration

1. **Disable Dangerous Functions**

    Add the following line to your `php.ini` file:

    ```ini
disable_functions = "exec,passthru,shell_exec"
```
   
2. **Set Up Open Basedir**

    Define a safe directory for PHP access by adding:

    ```ini
open_basedir = "/var/www/html:/etc/php/"
```

3. **Disable Allow URL Include**

    To disable remote file inclusion attempts:

    ```ini
allow_url_include=Off
```

4. **Run in a Docker Container**

    Ensure your application runs in an isolated container environment for added security.

    > [!SUCCESS] Example Command
    > ```bash
docker run --name my-php-app -v "$PWD":/var/www/html -p 80:80 php:7.4-apache
```

---

## 🛠️ Testing and Verification

### Verify Configuration Changes

After making changes to the PHP configuration, it's important to verify that they are correctly applied.

```bash
php -i | grep 'disable_functions'
php -i | grep 'open_basedir'
php -i | grep 'allow_url_include'
```

> [!CHECK] Command Output
> ```text
disable_functions => exec,passthru,shell_exec => disabled functions
open_basedir => /var/www/html:/etc/php/ => open_basedir
allow_url_include => Off => allow url include
```

### Container Isolation Verification

Ensure your application runs within a Docker container without issues:

```bash
docker ps | grep my-php-app
curl http://localhost
```

> [!SUCCESS] Expected Outcome
> The PHP application should be accessible via `http://localhost`, running in an isolated environment with security settings applied.

---

## 📄 Additional Resources

### Recommended Reading

- OWASP PHP Security Cheat Sheet: https://cheatsheetseries.owasp.org/cheatsheets/PHP_Security_Cheat_Sheet.html
- Docker Documentation for PHP Applications: https://docs.docker.com/samples/php/

> [!NOTE] Important Links
> These resources provide in-depth guidance and best practices for securing PHP applications using various techniques.

---

## 🧑‍💻 Conclusion

By configuring `disable_functions`, `open_basedir`, disabling `allow_url_include` and leveraging container isolation, you can significantly enhance the security posture of your PHP application. Regularly reviewing these settings and keeping up-to-date with new vulnerabilities is crucial for maintaining a secure environment.

> [!SUCCESS] Final Tip
> Remember to test configurations thoroughly in a development or staging environment before applying them to production systems.