?
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


