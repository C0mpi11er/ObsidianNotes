### IDS Detection Concepts — Summary Note

- **Network and host activity** can be classified into:
    
    - **Benign traffic/activity:** Normal, expected behavior that should not trigger alerts.
        
    - **Malicious traffic/activity:** Abnormal or unexpected behavior that the IDS should detect.
        
- IDS detection engines are designed around **how they identify threats**:
    
    - By recognizing **known malicious behavior**
        
    - Or by recognizing **normal behavior and detecting deviations**
        

---

### IDS Detection Engine Types

- **Signature-Based IDS**
    
    - Detects threats using **predefined signatures or rules**
        
    - Requires prior knowledge of malicious traffic patterns
        
    - Anything that matches a known signature is flagged as malicious
        
    - Commonly used in **anti-virus systems**
        
    - Assumption: _Anything not matching a malicious signature is benign_
        
- **Anomaly-Based IDS**
    
    - Detects threats by identifying **deviations from normal behavior**
        
    - Requires a baseline of normal traffic or activity
        
    - Baseline can be built using:
        
        - Machine learning
            
        - Manually defined rules
            
    - Assumption: _Anything deviating from normal is suspicious_