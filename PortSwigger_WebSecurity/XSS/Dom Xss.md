
?
==
DOM-based XSS vulnerabilities usually arise when JavaScript takes data from an attacker-controllable source, such as the URL, and passes it to a sink that supports dynamic code execution, such as `eval()` or `innerHTML`. This enables attackers to execute malicious JavaScript, which typically allows them to hijack other users' accounts.


how to find vuln
===
:>url
:>burp vuln scanner
:>place random string in search or input links and check dev tools in browser to see how input was consumed 
:>

limits
==
same sa relfected