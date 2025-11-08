
->> overview: basically techniques to mitigate getting detected or caught


->> host security solutions is a set of software that detect abnormal activities


    Antivirus software
    Microsoft Windows Defender
    Host-based Firewall
    Security Event Logging and Monitoring 
    Host-based Intrusion Detection System (HIDS)/ Host-based     Intrusion Prevention System (HIPS)
    Endpoint Detection and Response (EDR)



->> Anti virus : prevents malware and detects malware in real time 
techniques inlcude:  
    Signature-based detection:compares file with data base and flags if it matches.
    
    Heuristic-based detection:uses machine learning to detect and somtimes SBD(signature based) depends on the software implementation
    

    Behavior-based detection:watches for suspiscious activity like running process edititng registry etc 


As a red teamer, it is essential to be aware of whether antivirus exists or not. It prevents us from doing what we are attempting to do. We can enumerate AV software using Windows built-in tools, such as wmic.


command->> to find anti virus on host  Get-CimInstance -Namespace root/SecurityCenter2 -ClassName AntivirusProduct


check antivirus status
command ->> MpComputerStatus

disable fire wall
command ->> Set-NetFirewallProfile

checkfirewall blocks or check if port is open and listening
command->> Test-NetConnection
after checking invoke web request http://localhost/13337



event logs
command->Get-EventLog


for monitroting system event
 sysmon   
 check running process to know if its working or not


# HIDS
host based intrusin detection system 
utilizese signature and anomaly detection to find threats 


EDR (end point intrusion detection and response)
e.g crowdstrike,sentinelone