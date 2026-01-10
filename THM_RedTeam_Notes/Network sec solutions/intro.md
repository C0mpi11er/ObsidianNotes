
An Intrusion Detection System (IDS) is a system that detects network or system intrusions. One analogy that comes to mind is a guard watching live feeds from different security cameras. He can spot a theft, but he cannot stop it by himself. However, if this guard can contact another guard and ask them to stop the robber, detection turns into prevention. An Intrusion Detection and Prevention System (IDPS) or simply Intrusion Prevention System (IPS) is a system that can detect and prevent intrusions.

Understanding the difference between detection and prevention is essential. Snort is a network intrusion detection and intrusion prevention system. Consequently, Snort can be set up as an IDS or an IPS. For Snort to function as an IPS, it needs some mechanism to block (drop) offending connections. This capability requires Snort to be set up as inline and to bridge two or more network cards.


### IDS / IPS — Summary

- An **Intrusion Detection System (IDS)** monitors network or system activity to **detect** malicious behavior but cannot stop it.
    
- An **Intrusion Prevention System (IPS)** can both **detect and block** malicious activity.
    
- **Key difference:**
    
    - _Detection_ = alert only
        
    - _Prevention_ = detect + actively block
        
- **Snort** is a signature-based **network IDS/IPS**:
    
    - As an **IDS**, it passively monitors traffic and generates alerts.
        
    - As an **IPS**, it must be configured **inline**, bridging two or more network interfaces to **drop or block** malicious traffic.
        
- IDS deployments are categorized by location:
    
    - **Host-Based IDS (HIDS):** Installed on a host, monitors local traffic, processes, and system activity.
        
    - **Network-Based IDS (NIDS):** Dedicated system monitoring network traffic via mirror ports or taps, providing broad network visibility.
        
- **HIDS** offers deep insight into a single system, while **NIDS** provides wider coverage across networks or VLANs.