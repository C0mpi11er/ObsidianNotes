
A firewall is software or hardware that monitors the network traffic and compares it against a set of rules before passing or blocking it. One simple analogy is a guard or gatekeeper at the entrance of an event. This gatekeeper can check the ID of individuals against a set of rules before letting them enter (or leave).

Before we go into more details about firewalls, it is helpful to remember the contents of an IP packet and TCP segment. The following figure shows the fields we expect to find in an IP header. If the figure below looks complicated, you don’t need to worry as we are only interested in a few fields. Different types of firewalls are capable of inspecting various packet fields; however, the most basic firewall should be able to inspect at least the following fields:

    Protocol
    Source Address
    Destination Address
