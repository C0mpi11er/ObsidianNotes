JWTs are self-contained tokens that can be used to securely transmit session information.

has three parts:->
header: to identify the signing algorithm
payload:for holding information for a particular entity
signature:for token authenticity created with header algorithm 

types of signature algorithm 
=
none: meaning no signature algo was used to sign the jwt

symmetric signing : e.g H256
creates a signature when secret key is added to the header and body before signing any entity used for verification must know the secret key

asymmetric signing: e.g RS256
creates a signature when a private key is used to sign the header and the body and then a hash is created with the private key. entities that verify this algo must have a public key in relation to the private key used to hash the jwt


for decoding jwt https://jwt.io/


!!vulnerability
sensitive information disclosure :
=
getting password or clear text credentials from decoding the jwt token with the above website 

cmd:>curl -H 'Content-Type: application/json' -X POST -d '{ "username" : "user", "password" : "password1" }' http://10.66.180.78/api/v1.0/example1



Singnature not verified
=
remove the signature hash from the jwt to find out if server still responds normally if it does signature is not verified .
use the above website to edit claim and paste in command

cmd:> curl -H 'Authorization: Bearer [JWT Token]' http://10.66.180.78/api/v1.0/example2?username=user


Downgrade algo
=
must times the algorithm is not locked and can be manipulated
to "none" which requires no verification cyber chef and the above jwt.io can be used for editing
cyber check to base 64 edit the algo then copy back to jwt.io and tamper with admin claim to get informations required


weak jwt hash
=
weak jwt hash can be brutforce with hash cat
cmd:>hashcat -m 16500 -a 0 jwt.txt jwt.secrets.list

only work on HS256 
once the secret is know adversary can develop a new jwt admin token to login 



cross relay attacks
==
in this scenario one jwt can be used to authenticate more than one service
e.g if the jwt is accepted and certain privileges
are given from using one service it can be replicated to another service app A gives admin priv app B don't jwt for appA can be used to get admin priv in appB if it accepts the tokens





!! for confusion attacks
put pub key gotten in a file like nano pub_ssh.key

;> convert to pem 
ssh-keygen -f pub_ssh.key -e -m pem > pub.pem
;> check
openssl pkey -pubin -in pub.pem -text -noout
if openssl parses then its good


