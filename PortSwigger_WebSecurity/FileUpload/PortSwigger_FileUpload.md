File upload vulnerabilities are when a web server allows users to upload files to its filesystem without sufficiently validating things like their name, type, contents, or size. Failing to properly enforce restrictions on these could mean that even a basic image upload function can be used to upload arbitrary and potentially dangerous files instead. This could even include server-side script files that enable remote code execution.

In some cases, the act of uploading the file is in itself enough to cause damage. Other attacks may involve a follow-up HTTP request for the file, typically to trigger its execution by the server.


impacts of this vulnerability depends on two key factors
- Which aspect of the file the website fails to validate properly, whether that be its size, type, contents, and so on.

- What restrictions are imposed on the file once it has been successfully uploaded.

The `Content-Type` response header may provide clues as to what kind of file the server thinks it has served. If this header hasn't been explicitly set by the application code, it normally contains the result of the file extension/MIME type mapping.


# Blockades


============================================
## Flawed file type validation

When submitting HTML forms, the browser typically sends the provided data in a `POST` request with the content type `application/x-www-form-url-encoded`. This is fine for sending simple text like your name or address. However, it isn't suitable for sending large amounts of binary data, such as an entire image file or a PDF document. In this case, the content type `multipart/form-data` is preferred.
1. try changin the content typec in the multi form deposition form application/x-php to this the bracket value only
        
   Content-Disposition: form-data; name="avatar"; filename="simple-backdoor.php"
            vvvvvv    
{Content-Type: image/png}




# path tranversal
=======================================
As a precaution, servers generally only run scripts whose MIME type they have been explicitly configured to execute. Otherwise, they may just return some kind of error message or, in some cases, serve the contents of the file as plain text instead:

->>Try
1. find way to upload script to dir that isnt expecting user upload
2. 
->>> TIP
   Web servers often use the `filename` field in `multipart/form-data` requests to determine the name and location where the file should be saved.
   filename=../../../var/www/html/shell.php
   filename=..%2F%2F/shell.php   this obfuscate the path and then you can transverse and get shell
   
 ->> TIP   You should also note that even though you may send all of your requests to the same domain name, this often points to a reverse proxy server of some kind, such as a load balancer. Your requests will often be handled by additional servers behind the scenes, which may also be configured differently. 
 
->>>>>>>>>

->>check
2. this type of block sometimes provide way of leaking source code ...



===================================================
# insuficient black listing of dnagerouse files

One of the more obvious ways of preventing users from uploading malicious scripts is to blacklist potentially dangerous file extensions like `.php`. The practice of blacklisting is inherently flawed as it's difficult to explicitly block every possible file extension that could be used to execute code. Such blacklists can sometimes be bypassed by using lesser known, alternative file extensions that may still be executable, such as `.php5`, `.shtml`, and so on.


->> TRY overiding server config 
## Overriding the server configuration

As we discussed in the previous section, servers typically won't execute files unless they have been configured to do so. For example, before an Apache server will execute PHP files requested by a client, developers might have to add the following directives to their `/etc/apache2/apache2.conf` file:

`LoadModule php_module /usr/lib/apache2/modules/libphp.so AddType application/x-httpd-php .php`   


-->> check for web.config files , .htaccess files etc


# obfuscating file extension

->> .phar it executes php files and most webs haven't blacklisted it yet 

->> also sometimes obfuscate the . in the extension to bypass exploit%2Ephp or just .pHp case sensitive

->>> if validation is written in a high-level language like PHP or Java, but the server processes the file using lower-level functions in C/C++, for example, this can cause discrepancies in what is treated as the end of the

->> this works most of the time filename: exploit.asp;.jpg or exploit.asp%00.jpg

Try using multi byte Unicode characters, which may be converted to null bytes and dots after Unicode conversion or normalization. Sequences like xC0 x2E, xC4 xAE or xC0 xAE may be translated to x2E if the filename parsed as a UTF-8 string, but then converted to ASCII characters before being used in a path.    



Many servers also allow developers to create special configuration files within individual directories in order to override or add to one or more of the global settings. Apache servers, for example, will load a directory-specific configuration from a file called `.htaccess` if one is present.


if server does not remove extension recursively
try exploit.p.phphp


 ============================================
 ## Flawed validation of the file's contents

Instead of implicitly trusting the `Content-Type` specified in a request, more secure servers try to verify that the contents of the file actually match what is expected.

server use ->> for fingerprinting JPEG files, always begin with the bytes FF D8 FF. 


use 'exiftool' to manipulate digits or rather create a polygot jpeg add maliscious code to the data

->> check curl out put  with "-o -" for curl option the cmd answer usually shows at top you can use web browser for clearer view

tip ->> github the best place too get payloads ans shells 
# NOTE!!
for your sanity use simple-backdoor-php webshell

to find more data https://sallam.gitbook.io/sec-88/web-appsec/features-abuse/file-upload

https://swisskyrepo.github.io/PayloadsAllTheThings/Upload%20Insecure%20Files/#picture-compression

