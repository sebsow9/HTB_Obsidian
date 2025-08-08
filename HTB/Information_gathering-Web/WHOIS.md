---
Title: HTB\Information_gathering-Web\WHOIS
tags:
  - os/linux- cybersec
source: HTB\Information_gathering-Web\WHOIS
---

# HTB\Information_gathering-Web\WHOIS

> Źródło: `HTB\Information_gathering-Web\WHOIS`

WHOIS is a widely used query and response protcol designed to access databases that store information about registered internet resources. Primarily associated with domain names, WHOIS can also provide details about IP address blocks and autonomous systems. Think of it as a giant phonebook for the internet, letting you lookup who owns or is responsible for various online assets.

Each whois record typically contains the following information:
Domain Name: The domain name itself (e.g., example.com)
Registrar: The company where the domain was registered (e.g., GoDaddy, Namecheap)
Registrant Contact: The person or organization that registered the domain.
Administrative Contact: The person responsible for managing the domain.
Technical Contact: The person handling technical issues related to the domain.
Creation and Expiration Dates: When the domain was registered and when it's set to expire.
Name Servers: Servers that translate the domain name into an IP address.

Why WHOIS matter for web recon
Identifying Key Personnel: WHOIS records often reveal the names, email addresses, and phone numbers of individuals responsible for managing the domain. This information can be leveraged for social engineering attacks or to identify potential targets for phishing campaigns.
Discovering Network Infrastructure: Technical details like name servers and IP addresses provide clues about the target's network infrastructure. This can help penetration testers identify potential entry points or misconfigurations.
Historical Data Analysis: Accessing historical WHOIS records through services like WhoisFreaks can reveal changes in ownership, contact information, or technical details over time. This can be useful for tracking the evolution of the target's digital presence.

**Powiązane:** [[Graph-Home]] [[OS-Linux]]
