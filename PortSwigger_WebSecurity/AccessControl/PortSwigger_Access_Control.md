basically this vulnerability has do do with mainly privilege escalation of web user privileges either vertical or horizontal as the case maybe,
some web pages come with priv escalation,especially the once that are not secured enough .....
finding the "robots.txt" file most times hint on this disallowed directories

sometimes it is hidden in java script sour codes 
do well to check the as well e.g
`<script> var isAdmin = false; if (isAdmin) { ... var adminPanelTag = document.createElement('a'); adminPanelTag.setAttribute('href', 'https://insecure-website.com/administrator-panel-yb556'); adminPanelTag.innerText = 'Admin panel'; ... } </script>`


Some applications determine the user's access rights or role at login, and then store this information in a user-controllable location. This could be:

- A hidden field.
- A cookie.
- A preset query string parameter.

The application makes access control decisions based on the submitted value. For example:

`https://insecure-website.com/login/home.jsp?admin=true https://insecure-website.com/login/home.jsp?role=1`

This approach is insecure because a user can modify the value and access functionality they're not authorized to, such as administrative functions.

GUID Ref

in some applications, the exploitable parameter does not have a predictable value. For example, instead of an incrementing number, an application might use globally unique identifiers (GUIDs) to identify users. This may prevent an attacker from guessing or predicting another user's identifier. However, the GUIDs belonging to other users might be disclosed elsewhere in the application where users are referenced, such as user messages or reviews.

could also be names or namubers e.g
https://vulnerbalewbesite/user?id=123 or "Dante" 


some headers used to overwrite allowed methods
`X-Original-URL` and `X-Rewrite-URL`


:>most times check the http methodes
:>some access control vuln lay init 
:>ADMIN/DELETEUSER and admin/deletuser can be treated as diff end points
:>for prior to spring frame work 5.3 "usesuffixpatternmatch" is enabled by defaults suffixed e.g deleteuser.anything might still lead to delete user 

:>horizontal and vertical priv escaltion by changing url params 

:>if its multi step process check if you can bypass any of the stages 

:>access control sometimes might be based on referer header 

:>geo location based access control where some web app restrict certain users from locations to access some resources can be mitigated web proxies vpns etc




How to prevent access control vulnerabilities
=

Access control vulnerabilities can be prevented by taking a defense-in-depth approach and applying the following principles:

    Never rely on obfuscation alone for access control.
    Unless a resource is intended to be publicly accessible, deny access by default.
    Wherever possible, use a single application-wide mechanism for enforcing access controls.
    At the code level, make it mandatory for developers to declare the access that is allowed for each resource, and deny access by default.
    Thoroughly audit and test access controls to ensure they work as designed.
