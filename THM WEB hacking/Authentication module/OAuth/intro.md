In modern web applications, OAuth vulnerabilities emerge as a serious and frequently disregarded risk; when we talk about OAuth, we're talking about OAuth 2.0, the commonly used authorisation framework. The vulnerabilities occur when hackers take advantage of weaknesses in OAuth 2.0, which allows for CSRF, XSS, data leakage and exploitation of other vulnerabilities.

concepts
==

resource owner:person using the app

client: the mobile app or server side web application
auth server:issues tokens to the client to access resources

scope:monitors clients privileges

state parameter:uses to stop csrf and xss etc as it makes server response inclined with request

refresh token:useful not to allow constat login when user still using site so refresh token is uses to get new access token
access token :user to grant priviledges to access resources

redirct url: on accepts of rejecting of credentials this si used to redirect client to respective pages

server auth/or auth server: receives protected request and grants tokens 


auth grant types
=
code auth grant:uses code sent by client on inputong details  to get access token and get resources

implicit grant: no auth code sent  and client signes in access token is granted and next request constains access tokens 

client credential grants:mostly used for backened services as no client is involved an auth is done through application secrets to access resources

resource owner  credentials grant: mostly used by web apps as credentials is only needed by auth server to grant access toekn to access resources 
