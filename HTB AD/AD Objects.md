We will often see the term "objects" when referring to AD. What is an object? An object can be defined as ANY resource present within an Active Directory environment such as OUs, printers, users, domain controllers.

**Users** — Leaf security principals with SID & GUID; many attributes; prime attack targets.

**Contacts** — External people records; leaf objects; GUID only; no SID.

**Printers** — AD-published printers; leaf objects; GUID only.

**Computers** — Domain-joined machines; leaf objects; SID & GUID; major attack surface.

**Shared Folders** — AD-listed file shares; GUID only; access controlled via ACLs.

**Groups** — Containers of users/computers/groups; security principals; enable permission management; nesting can create attack paths.

**Organizational Units (OUs)** — Admin containers for delegation and GPO scoping.

**Domain** — Core AD boundary containing objects and policies.

**Domain Controllers (DCs)** — Authenticate users and enforce security; store AD database.

**Sites** — Subnet-based groupings optimizing DC replication.

**Built-in** — Container holding default domain groups.

**Foreign Security Principals (FSPs)** — Placeholder objects for trusted-forest accounts; store foreign SIDs.