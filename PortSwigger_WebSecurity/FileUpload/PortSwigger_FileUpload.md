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
# NOTE!!
for your sanity use simple-backdoor-php webshell


