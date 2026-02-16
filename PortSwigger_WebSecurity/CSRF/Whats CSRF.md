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

This situation is harder to exploit but is still vulnerable. If the website contains any behavior that allows an attacker to set a cookie in a victim's browser, then an attack is possible. The attacker can log in to the application using their own account, obtain a valid token and associated cookie, leverage the cookie-setting behavior to place their cookie into the victim's browser, and feed their token to the victim in their CSRF attack.



in changing cookie `/?search=test%0d%0aSet-Cookie:%20csrfKey=YOUR-KEY%3b%20SameSite=None`


after removing the sxript block

`<img src="https://YOUR-LAB-ID.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrfKey=YOUR-KEY%3b%20SameSite=None" onerror="document.forms[0].submit()">`


->> sometimes csrf is a duplicated cookies
->> recreate cookies setting behaviour and use it to perform csrf 

`POST /email/change HTTP/1.1 Host: vulnerable-website.com Content-Type: application/x-www-form-urlencoded Content-Length: 68 Cookie: session=1DQGdzYbOJQzLP7460tfyiv3do7MjyPw; csrf=R8ov2YBfTYmzFyjit8o2hKBuoIjXXVpa csrf=R8ov2YBfTYmzFyjit8o2hKBuoIjXXVpa&email=wiener@normal-user.com`


In this situation, the attacker can again perform a CSRF attack if the website contains any cookie setting functionality. Here, the attacker doesn't need to obtain a valid token of their own. They simply invent a token (perhaps in the required format, if that is being checked), leverage the cookie-setting behavior to place their cookie into the victim's browser, and feed their token to the victim in their CSRF attack.

 