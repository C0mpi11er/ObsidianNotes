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


