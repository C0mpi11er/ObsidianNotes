z?
==
Reflected cross-site scripting (or XSS) arises when an application receives data in an HTTP request and includes that data within the immediate response in an unsafe way

way to find reflected xss
==
:>test every entry point
:>submit random values
:>determine the reflection context 
:>test candidate payload 
:> test alternative payloads incase first one was blocked
:>test attack in browser


->> limitations
- The victim might not be logged in.
- Many applications hide their cookies from JavaScript using the `HttpOnly` flag.
- Sessions might be locked to additional factors like the user's IP address.
- The session might time out before you're able to hijack it.


poc 
<script>alert(document.domain)</script>
<img src=1 onerror=alert(1)>

findings
=

1. some reflected xss vuln require enumarating through tags and events then using iframe payloads with accepted tags and events e.g
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/?search=%22%3E%3Cbody%20onresize=print()%3E" onload=this.style.width='100px'>

note# during enumarating add the space in url encode body%30<event>


2. when using custom tags makes sure you exhaust all possible event payloads available  with the xss cheat sheet then foscus on the once that require no user interaction to execute e.g <xss autofocus tabindex=1 onfocus=alert(1)></xss>
<xss autofocus tabindex=1 onfocusin=alert(1)></xss>

when using custom server like burpe may sure payload is in script tags <script></script>

