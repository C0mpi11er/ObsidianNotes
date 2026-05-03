Nmap scan report for inlanefreight.local (172.16.1.11)
Host is up (0.37s latency).
Not shown: 988 closed tcp ports (conn-refused)
PORT     STATE SERVICE
80/tcp   open  http
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
515/tcp  open  printer
1801/tcp open  msmq
2103/tcp open  zephyr-clt
2105/tcp open  eklogin
2107/tcp open  msmq-mgmt
3389/tcp open  ms-wbt-server
5985/tcp open  wsman
8080/tcp open  http-proxy
Aggressive OS guesses: 3Com Baseline Switch 2924-SFP or Cisco ESW-520 switch or Allied Telesis AT-8000 series switch (85%), Allied Telesis AT-8000S; Dell PowerConnect 2824, 3448, 5316M, or 5324; Linksys SFE2000P, SRW2024, SRW2048, or SRW224G4; or TP-LINK TL-SL3428 switch (85%), Aruba, Cisco, or Netgear switch (Linux 3.10 or 4.4) (85%), Linksys SRW2008MP switch (85%), Cisco SG 300-10, Dell PowerConnect 2748, Linksys SLM2024, SLM2048, or SLM224P, or Netgear FS728TP or GS724TP switch (85%), Linksys SRW2000-series or Allied Telesyn AT-8000S switch (85%), IBM z/OS 1.12 (85%)
No exact OS matches for host (test conditions non-ideal).




autorecon 10.10.20.15 \
-p 80,135,139,445,515,1801,2103,2105,2107,3389,5985,8080 \
-m 5 \
--max-port-scans 1 \
--nmap "-sT -sV -Pn -T2 --max-retries 1" \
--exclude-tags brute,dirbuster \
-o 172.16.1.11_pivot_scan