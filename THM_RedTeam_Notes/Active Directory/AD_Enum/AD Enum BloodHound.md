# 🩸 Bloodhound & SharpHound for AD Enumeration

**Bloodhound** is the most powerful AD enumeration tool to date, visualizing the AD environment as a **graph** rather than lists. This shift in perspective ("Defenders think in lists, Attackers think in graphs") allows for finding exploitable **attack paths** (edges) between users, hosts, and groups (nodes).



***

## 1. Key Components

| Tool            | Function              | Description                                                                                                              |
| --------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| **SharpHound  * | **Data Collector**    | Enumerates AD information and packages it into a ZIP file (JSON format).                                                 |
| **Bloodhoun  ** | **Visualization/GUI** | Imports the SharpHound data and displays the AD environment and attack paths using the **Neo4j** graph database backend. |

***

## 2. SharpHound Collection Methods

SharpHound is the enumeration tool. It has three versions (`.ps1`, `.exe`, `AzureHound.ps1`).

| Parameter               | Purpose                                                                              | Common Values                                                                       |
| ----------------------- | ------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------- |
| `--CollectionMethods  ` | Determines what data is collected.                                                   | `Default`, **`All`**, **`Session`** (only collects new user sessions, much faster). |
| `--Domain               | Specifies the AD domain to enumerate.                                                | `za.tryhackme.com` (Example)                                                        |
| `--ExcludeDC            | Reduces the attack noise by instructing SharpHound not to touch Domain Controllers.  |                                                                                     |

> **Note:** SharpHound binaries are often detected as malware. A common operational security practice is to run the tool from a **non-domain-joined Windows machine** using the `runas` command and specific AD credentials.

***

## 3. Bloodhound Usage & Analysis

1.  **Start Neo4j:** The graph database backend must be running (`neo4j console start`).
2.  **Launch Bloodhound:** Authenticate using Neo4j credentials (default: `neo4j:neo4j`).
3.  **Import Data:** Transfer the SharpHound ZIP file to the attacking machine and drag/drop it into the Bloodhound GUI.

### Analysis Queries (Attack Paths)

Bloodhound allows you to search for the shortest path between a **Start Node** (e.g., your compromised AD user) and an **End Node** (e.g., the `DOMAIN ADMINS` group).

* **Nodes (Icons):** Represent AD objects (**Users, Groups, Computers, GPOs**, etc.).
* **Edges (Lines):** Represent a potential exploitable connection or right (e.g., `MemberOf`, **`AdminTo`**, **`CanRDP`**).

**Example Attack Path Logic:**
* **Path:** `AD User` → `RDP` → `Workstation (THMJMP1)` → `Active Session` → `T1 Admin Account`
* **Exploitation:** RDP into the workstation, escalate privileges to Admin, harvest the T1 Admin's credentials (e.g., using **Mimikatz**) from their active session.

### Node Information

Clicking on a Node (object) in Bloodhound provides detailed information across various categories:

* **Group Membership:** Groups the account belongs to.
* **Local Admin Rights:** Hosts where the account has administrative privileges.
* **Execution Rights:** Special privileges (e.g., ability to RDP or run commands).
* **Outbound/Inbound Control Rights:** Permissions to modify/be modified by other AD objects.

***

## 4. Session Data Management

* **AD Structure:** Changes slowly. Initial run should use **`All`** methods.
* **Active Sessions:** Change constantly. Subsequent runs should use the **`Session`** collection method (e.g., twice daily) to quickly update active logon data.
* **Clearing:** Use the "**Clear Session Information**" button in the Database Info tab before importing new session-only runs to prevent stale data.

***

## 5. Benefits & Drawbacks

| Benefits                                                            | Drawbacks                                                                               |
| ------------------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| Provides a **GUI** for complex AD enumer                            | Requires execution of **SharpHound**, which is **noisy** and often flagged by AV/EDR.   |
| Finds **exploit-ready attack paths** with minimal manua   e  fo  t. | Requires matching versions of Bloodhound and SharpHound for successful data ingestion.  |
| Offers **in-depth insights** into AD object re                      |                                                                                         |

name; neo4j
pass:12345678

note! launch neo first with "sudo neo4j console"