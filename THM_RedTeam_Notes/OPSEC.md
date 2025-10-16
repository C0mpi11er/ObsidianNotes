
if the adversary discovers that you are scanning their network with Nmap (the blue team in our case), they should easily be able to discover the IP address used. For instance, if you use this same IP address to host a phishing site, it won’t be very difficult for the blue team to connect the two events and attribute them to the same actor.

OPSEC is not a solution or a set of rules; OPSEC is a five-step process to deny adversaries from gaining access to any critical information (defined in Task 2). We will dive into each step and see how we can improve OPSEC as part of our red team operations.

What a red teamer considers critical information worth protecting depends on the operation and the assets or tooling used. In this setting, critical information includes, but is not limited to, the red team’s intentions, capabilities, activities, and limitations. Critical information includes any information that, once obtained by the blue team, would hinder or degrade the red team’s mission.
![[opsec.png]]

After we identify critical information, we need to analyse threats. _Threat analysis refers to identifying potential adversaries and their intentions and capabilities_. Adapted from the US Department of Defense [(DoD) Operations Security (OPSEC) Program Manual](https://www.esd.whs.mil/Portals/54/Documents/DD/issuances/dodm/520502m.pdf), threat analysis aims to answer the following questions:

1. Who is the adversary?
2. What are the adversary’s goals?
3. What tactics, techniques, and procedures does the adversary use?
4. What critical information has the adversary obtained, if any?

![[adversary.png]]

After identifying critical information and analysing threats, we can start with the third step: analysing vulnerabilities. This is not to be confused with vulnerabilities related to cybersecurity. An _OPSEC vulnerability exists when an adversary can obtain critical information, analyse the findings, and act in a way that would affect your plans._

![[opsecvuln.png]]


We finished analysing the vulnerabilities, and now we can proceed to the fourth step: conducting a risk assessment. [NIST](https://csrc.nist.gov/glossary/term/risk_assessment) defines a risk assessment as "The process of identifying risks to organizational operations (including mission, functions, image, reputation), organizational assets, individuals, other organizations, and the Nation, resulting from the operation of an information system." In OPSEC, risk assessment requires learning the possibility of an event taking place along with the expected cost of that event. Consequently, this involves assessing the adversary’s ability to exploit the vulnerabilities.


Once the level of risk is determined, countermeasures can be considered to mitigate that risk. We need to consider the following three factors:

1. The efficiency of the countermeasure in reducing the risk
2. The cost of the countermeasure compared to the impact of the vulnerability being exploited.
3. The possibility that the countermeasure can reveal information to the adversary