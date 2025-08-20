Snort is one of the most widely used open-source IDS solutions developed in 1998. It uses signature-based and anomaly-based detections to identify known threats. These are defined in the rule files of the Snort tool. Several built-in rule files come pre-installed in this tool’s package. These built-in rule files contain a variety of known attack patterns. Snort’s built-in rules can detect a lot of malicious traffic for you. However, you can configure Snort to detect specific types of network traffic that are not covered by the default rule files. You can create custom rules based on your requirements to detect specific traffic. You can also disable any built-in detection rules if they don’t point to harmful traffic for your system or network and define some custom rules instead. In the upcoming task, we will explore the built-in rules and make custom rules to detect specific traffic.


![[Snortconfig.png]]
^ rules config format

file mst be in /etc/snort/rule/local.rules
`alert icmp any any -> 127.0.0.1 any (msg:"Loopback Ping Detected"; sid:10003; rev:1;)`


*Detect intrusion*
starting snort: ~$ sudo snort -q -l /var/log/snort -i lo[custom] -A console -c /etc/snort/snort.conf

*Read From Packet capture Files*
sudo snort -q -l /var/log/snort -r Task.pcap -A console -c /etc/snort/snort.conf