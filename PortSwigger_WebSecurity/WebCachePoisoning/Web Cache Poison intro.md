 Web cache poisoning is an advanced technique whereby an attacker exploits the behavior of a web server and cache so that a harmful HTTP response is served to other users.

Fundamentally, web cache poisoning involves two phases. First, the attacker must work out how to elicit a response from the back-end server that inadvertently contains some kind of dangerous payload. Once successful, they need to make sure that their response is cached and subsequently served to the intended victims.

A poisoned web cache can potentially be a devastating means of distributing numerous different attacks, exploiting vulnerabilities such as XSS, JavaScript injection, open redirection, and so on. 

Exploiting Cache Design Flaws
==
## Core Idea  
Web Cache Poisoning happens when:  
  
- A server uses **user-controlled input**  
- The input is **not included in the cache key (unkeyed input)**  
- The input is **reflected in a cacheable response**  
  
Result:    
A **malicious response gets cached** and is served to **all users requesting that page**.  
  
---  
  
## Common Unkeyed Inputs  
Headers often used in attacks:  
  
- `X-Forwarded-Host`  
- `X-Forwarded-Proto`  
- `Host`  
- `X-Original-URL`  
  
---  
  
## Example: Cache Poisoning with XSS  
  
### Normal Request

GET /en?region=uk HTTP/1.1  
Host: innocent-website.com  
X-Forwarded-Host: innocent-website.co.uk

  
### Server Response

HTTP/1.1 200 OK  
Cache-Control: public  
<meta property="og:image" content="https://innocent-website.co.uk/cms/social.png" />

  
The server **uses the header value to generate a URL**.  
  
---  
  
### Attacker Poisoning Request

GET /en?region=uk HTTP/1.1  
Host: innocent-website.com  
X-Forwarded-Host: a."><script>alert(1)</script>"

  
### Cached Poisoned Response

<meta property="og:image" content="https://a."><script>alert(1)</script>"/cms/social.png" />

  
---  
  
## Impact  
Anyone visiting:  

/en?region=uk

  
gets the **cached XSS payload**.  
  
Possible attacker actions:  
  
- Steal cookies  
- Hijack sessions  
- Run malicious JavaScript  
- Perform phishing attacks  
  
---  
  
## Attack Logic

Unkeyed Input  
+  
Reflected in Response  
+  
Cacheable Response  
=  
Web Cache Poisoning