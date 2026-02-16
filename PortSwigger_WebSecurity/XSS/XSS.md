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