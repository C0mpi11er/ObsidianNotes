Data exfiltration is a non-traditional approach for copying and transferring data from a compromised to an attacker's machine. The data exfiltration technique is used to emulate the normal network activities, and It relies on network protocols such as DNS, HTTP, SSH, etc. Data Exfiltration over common protocols is challenging to detect and distinguish between legitimate and malicious traffic.

Some protocols are not designed to carry data over them. However, threat actors find ways to abuse these protocols to bypass network-based security products such as a firewall. Using these techniques as a red teamer is essential to avoid being detected


Sensitive data can be in various types and forms, and it may contain the following:

    Usernames and passwords or any authentication information.
    Bank accounts details
    Business strategic decisions.
    Cryptographic keys.
    Employee and personnel information.
    Project code data.



use cases of data exfiltration
==
tunneling: no limit of request and response and over traditional tcp protocols,continuous traffick

command and control : response and request available but over non traditional protocols

exfiltrate data: limited data and one sided request no response

