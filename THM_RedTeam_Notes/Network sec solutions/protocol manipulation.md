

Evading a signature-based IDS/IPS requires that you manipulate your traffic so that it does not match any IDS/IPS signatures. Here are four general approaches you might consider to evade IDS/IPS systems.

    Evasion via Protocol Manipulation
    Evasion via Payload Manipulation
    Evasion via Route Manipulation
    Evasion via Tactical Denial of Service (DoS)


This room focuses on evasion using nmap and ncat/socat. The evasion techniques related to Nmap are discussed in great detail in the Firewalls room. This room will emphasize ncat and socat where appropriate.

We will expand on each of these approaches in its own task. Let’s start with the first one. Evasion via protocol manipulation includes:

    Relying on a different protocol
    Manipulating (Source) TCP/UDP port
    Using session splicing (IP packet fragmentation)
    Sending invalid packets


Rely on a Different Protocol

The IDS/IPS system might be configured to block certain protocols and allow others. For instance, you might consider using UDP instead of TCP or rely on HTTP instead of DNS to deliver an attack or exfiltrate data. You can use the knowledge you have gathered about the target and the applications necessary for the target organization to design your attack. For instance, if web browsing is allowed, it usually means that protected hosts can connect to ports 80 and 443 unless a local proxy is used. In one case, the client relied on Google services for their business, so the attacker used Google web hosting to conceal his malicious site. Unfortunately, it is not a one-size-fits-all; moreover, some trial and error might be necessary as long as you don’t create too much noise.

We have an IPS set to block DNS queries and HTTP requests in the figure below. In particular, it enforces the policy where local machines cannot query external DNS servers but should instead query the local DNS server; moreover, it enforces secure HTTP communications. It is relatively permissive when it comes to HTTPS. In this case, using HTTPS to tunnel traffic looks like a promising approach to evade the IPS.


Consider the case where you are using Ncat. Ncat, by default, uses a TCP connection; however, you can get it to use UDP using the option -u.

    To listen using TCP, just issue ncat -lvnp PORT_NUM where port number is the port you want to listen to.
    to connect to an Ncat instance listening on a TCP port, you can issue ncat TARGET_IP PORT_NUM

Note that:

    -l tells ncat to listen for incoming connections
    -v gets more verbose output as ncat binds to a source port and receives a connection
    -n avoids resolving hostnames
    -p specifies the port number that ncat will listen on

As already mentioned, using -u will move all communications over UDP.

    To listen using UDP, just issue ncat -ulvnp PORT_NUM where port number is the port you want to listen to. Note that unless you add -u, ncat will use TCP by default.
    To connect to an Ncat instance listening on a UDP port, you can issue nc -u TARGET_IP PORT_NUM

Consider the following two examples:

    Running ncat -lvnp 25 on the attacker system and connecting to it will give the impression that it is a usual TCP connection with an SMTP server, unless the IDS/IPS provides deep packet inspection (DPI).
    Executing ncat -ulvnp 162 on the attacker machine and connecting to it will give the illusion that it is a regular UDP communication with an SNMP server unless the IDS/IPS supports DPI.

Manipulate (Source) TCP/UDP Port

Generally speaking, the TCP and UDP source and destination ports are inspected even by the most basic security solutions. Without deep packet inspection, the port numbers are the primary indicator of the service used. In other words, network traffic involving TCP port 22 would be interpreted as SSH traffic unless the security solution can analyze the data carried by the TCP segments.

Depending on the target security solution, you can make your port scanning traffic resemble web browsing or DNS queries. If you are using Nmap, you can add the option -g PORT_NUMBER (or --source-port PORT_NUMBER) to make Nmap send all its traffic from a specific source port number.

While scanning a target, use nmap -sS -Pn -g 80 -F 10.64.151.184 to make the port scanning traffic appear to be exchanged with an HTTP server at first glance.

If you are interested in scanning UDP ports, you can use nmap -sU -Pn -g 53 -F 10.64.151.184 to make the traffic appear to be exchanged with a DNS server.


Consider the case where you are using Ncat. You can try to camouflage the traffic as if it is some DNS traffic.

    On the attacker machine, if you want to use Ncat to listen on UDP port 53, as a DNS server would, you can use ncat -ulvnp 53.
    On the target, you can make it connect to the listening server using ncat -u ATTACKER_IP 53.

Alternatively, you can make it appear more like web traffic where clients communicate with an HTTP server.

    On the attacker machine, to get Ncat to listen on TCP port 80, like a benign web server, you can use ncat -lvnp 80.
    On the target, connect to the listening server using nc ATTACKER_IP 80.


Use Session Splicing (IP Packet Fragmentation)

Another approach possible in IPv4 is IP packet fragmentation, i.e., session splicing. The assumption is that if you break the packet(s) related to an attack into smaller packets, you will avoid matching the IDS signatures. If the IDS is looking for a particular stream of bytes to detect the malicious payload, divide your payload among multiple packets. Unless the IDS reassembles the packets, the rule won’t be triggered.

Nmap offers a few options to fragment packets. You can add:

    -f to set the data in the IP packet to 8 bytes.
    -ff to limit the data in the IP packet to 16 bytes at most.
    --mtu SIZE to provide a custom size for data carried within the IP packet. The size should be a multiple of 8.

Suppose you want to force all your packets to be fragmented into specific sizes. In that case, you should consider using a program such as Fragroute. fragroute can be set to read a set of rules from a given configuration file and applies them to incoming packets. For simple IP packet fragmentation, it would be enough to use a configuration file with ip_frag SIZE to fragment the IP data according to the provided size. The size should be a multiple of 8.

For example, you can create a configuration file fragroute.conf with one line, ip_frag 16, to fragment packets where IP data fragments don’t exceed 16 bytes. Then you would run the command fragroute -f fragroute.conf HOST. The host is the destination to which we would send the fragmented packets it.
Sending Invalid Packets

Generally speaking, the response of systems to valid packets tends to be predictable. However, it can be unclear how systems would respond to invalid packets. For instance, an IDS/IPS might process an invalid packet, while the target system might ignore it. The exact behavior would require some experimentation or inside knowledge.

Nmap makes it possible to create invalid packets in a variety of ways. In particular, two common options would be to scan the target using packets that have:

    Invalid TCP/UDP checksum
    Invalid TCP flags

Nmap lets you send packets with a wrong TCP/UDP checksum using the option --badsum. An incorrect checksum indicates that the original packet has been altered somewhere across its path from the sending program.

Nmap also lets you send packets with custom TCP flags, including invalid ones. The option --scanflags lets you choose which flags you want to set.

    URG for Urgent
    ACK for Acknowledge
    PSH for Push
    RST for Reset
    SYN for Synchronize
    FIN for Finish

For instance, if you want to set the flags Synchronize, Reset, and Finish simultaneously, you can use --scanflags SYNRSTFIN, although this combination might not be beneficial for your purposes.

If you want to craft your packets with custom fields, whether valid or invalid, you might want to consider a tool such as hping3. We will list a few example options to give you an idea of packet crafting using hping3.

    -t or --ttl to set the Time to Live in the IP header
    -b or --badsum to send packets with a bad UDP/TCP checksum
    -S, -A, -P, -U, -F, -R to set the TCP SYN, ACK, PUSH, URG, FIN, and RST flags, respectively

There is a myriad of other options. Depending on your needs, you might want to check the hping3 manual page for the complete list.