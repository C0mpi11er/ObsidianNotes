

?
==
Stored cross-site scripting (also known as second-order or persistent XSS) arises when an application receives data from an untrusted source and includes that data within its later HTTP responses in an unsafe way.


how to find it 
==
:>using burpe vuln scanner
:>url string or file,http headers,out of bound routes 
::>find the link between entry and exit point

limitations
==
same as reflected xss