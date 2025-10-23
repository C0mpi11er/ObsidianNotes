
passive recon : no interaction
active recon : interaction to provoke and see response level


active recon further broken into: 1. **External Recon**: Conducted outside the target's network and focuses on the externally facing assets assessable from the Internet. One example is running Nikto from outside the company network.

2. **Internal Recon**: Conducted from within the target company's network. In other words, the pentester or red teamer might be physically located inside the company building. In this scenario, they might be using an exploited host on the target's network. An example would be using Nessus to scan the internal network using one of the target’s computers.


# whois
this is a request and response protocol 
used to query domain register for info
creation date register url etc


# nslookup
->> ns lookup -url
get the A and AAAA names of a domain

# Dig
->> dig -url
domain info gropper ..also does same domain passive recon to get info 

# Host 
another useful tool ->> host -url


# traceroute
trace rout used to get inof about hops packest followed from host to destination
note!! some routers dont respond to rquest from traceroute or tracert as the case maybe ..the respond with a* 





# using search engine syntax 
# How to use advanced syntax on DuckDuckGo Search

DuckDuckGo supports search syntax you can use to fine-tune your queries.

## Search Operators

|                          |                                                                                                                                                            |
| ------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Example                  | Result                                                                                                                                                     |
| `cats dogs`              | Results about cats or dogs                                                                                                                                 |
| `"cats and dogs"`        | Results for exact term "cats and dogs". If no or few results are found, we'll try to show related results.                                                 |
| `~"cats and dogs"`       | Experimental syntax: more results that are semantically similar to "cats and dogs", like "cats & dogs" and "dogs and cats" in addition to "cats and dogs". |
| `cats -dogs`             | Fewer dogs in results                                                                                                                                      |
| `cats +dogs`             | More dogs in results                                                                                                                                       |
| `cats filetype:pdf`      | PDFs about cats. Supported file types: pdf, doc(x), xls(x), ppt(x), html                                                                                   |
| `dogs site:example.com`  | Pages about dogs from example.com                                                                                                                          |
| `cats -site:example.com` | Pages about cats, excluding example.com                                                                                                                    |
| `intitle:dogs`           | Page title includes the word "dogs"                                                                                                                        |
| `inurl:cats`             | Page URL includes the word "cats"                                                                                                                          |



# social media 
makes it easy to get info about a person as most people share alot of self info about social media e.g place of work etc


# job ads
also give information about a company 



# viewdns.info 
view dns info is for revers ip look up to actually know the other sites that maybe sharing server with target ...as ip may not lead direclty to target



# threat intelligence plateform
does same thing as viewdns just better and searches for malware and presents all details in a more apealing way 



# #### Censys
used to get info on ip adresses and ports that are open 






# recon-ng
[Recon-ng](https://github.com/lanmaster53/recon-ng) is a framework that helps automate the OSINT work. It uses modules from various authors and provides a multitude of functionality. Some modules require keys to work; the key allows the module to query the related online API. In this task, we will demonstrate using Recon-ng in the terminal.

From a penetration testing and red team point of view, Recon-ng can be used to find various bits and pieces of information that can aid in an operation or OSINT task. All the data collected is automatically saved in the database related to your workspace. For instance, you might discover host addresses to later port-scan or collect contact email addresses for phishing attacks.

Before you install modules using the marketplace, these are some useful commands related to marketplace usage:

- `marketplace search KEYWORD` to search for available modules with _keyword_.
- `marketplace info MODULE` to provide information about the module in question.
- `marketplace install MODULE` to install the specified module into Recon-ng.
- `marketplace remove MODULE` to uninstall the specified module.






# Maltego 

Maltego is an application that blends mind-mapping with OSINT. In general, you would start with a domain name, company name, person’s name, email address, etc. Then you can let this piece of information go through various transforms.

The information collected in Maltego can be used for later stages. For instance, company information, contact names, and email addresses collected can be used to create very legitimate-looking phishing emails.

Think of each block on a Maltego graph as an entity. An entity can have values to describe it. In Maltego’s terminology, a transform is a piece of code that would query an API to retrieve information related to a specific entity. The logic is shown in the figure below. Information related to an entity goes via a transform to return zero or more entities.

It is crucial to mention that some of the transforms available in Maltego might actively connect to the target system. Therefore, it is better to know how the transform works before using it if you want to limit yourself to passive reconnaissance.