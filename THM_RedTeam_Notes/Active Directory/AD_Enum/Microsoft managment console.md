# 🔍 Active Directory: Enumeration via MMC (RSAT Snap-Ins)

## 💡 Goal
Use the **Microsoft Management Console (MMC)** and the **Remote Server Administration Tools (RSAT)** Active Directory Snap-Ins to visually enumerate the domain structure using injected credentials.

## 🛠️ Setup & Execution
1.  **Requirement:** Must be run from a **Windows machine** with **RSAT** installed.
2.  **Authentication:** Launch MMC from the previously established **`runas.exe /netonly`** command prompt to ensure all network connections authenticate using the injected AD credentials.
    ```bash
    runas.exe /netonly /user:<FQDN>\<username> mmc.exe
    ```
3.  **Add Snap-Ins:** In MMC (File -> Add/Remove Snap-in), add the following:
    * Active Directory Domains and Trusts
    * Active Directory Sites and Services
    * **Active Directory Users and Computers** (The primary tool)
4.  **Configure Domain:** Right-click on the Snap-ins and change the domain/forest root to the target FQDN (e.g., `za.tryhackme.com`).
5.  **Enable Advanced Features:** In AD Users and Computers, go to **View -> Advanced Features** to expose hidden containers and property tabs for in-depth analysis.

## 🌲 Enumeration Structure
The GUI allows for a tree-like exploration of the AD hierarchy:

* **Organizational Units (OUs):** Expand the domain to view containers like **People** (users), **Servers**, and **Workstations**.
* **Users:** Click on user OUs to list accounts. Double-click a user to view:
    * All **properties/attributes** (Description, Notes, SPNs).
    * **Group Membership** (crucial for privilege escalation).
* **Hosts:** List domain-joined machines under the **Servers** or **Workstations** OUs.

## ⚖️ Benefits vs. Drawbacks

| Benefits (Pros) | Drawbacks (Cons) |
| :--- | :--- |
| **Holistic View:** Excellent graphical representation of the AD structure. | **RDP Requirement:** Requires remote desktop access to a Windows machine. |
| **Rapid Search:** Quick and easy to find individual objects. | **Inefficient for Mass Data:** Poor for large-scale attribute gathering across the domain. |
| **Direct Management:** Allows direct modification of objects if privileges are sufficient. | **Manual Process:** Click-intensive and slow for comprehensive enumeration. |