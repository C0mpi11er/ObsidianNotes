## 🧠 BLOODHOUND = GRAPH THEORY

You’re analyzing:

Nodes: Users, Groups, Computers  
Edges: Permissions, Sessions, ACLs

---

## 🔥 HIGH-VALUE EDGES (MEMORIZE THESE)

These are **real attack gold**:

|Edge|Meaning|Attack|
|---|---|---|
|`GenericAll`|Full control|Reset password|
|`GenericWrite`|Modify attributes|Add SPN / Shadow creds|
|`WriteOwner`|Take ownership|Full takeover|
|`WriteDACL`|Modify permissions|Grant yourself control|
|`HasSession`|Admin logged in|Token theft|
|`AdminTo`|Local admin rights|Dump creds|
|`ForceChangePassword`|Reset password|Take account|