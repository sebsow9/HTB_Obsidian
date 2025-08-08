---
Title: HTB\Information_gathering-Web\Subdomains
tags:
  - os/unknown- cybersec
source: HTB\Information_gathering-Web\Subdomains
---

# HTB\Information_gathering-Web\Subdomains

> Źródło: `HTB\Information_gathering-Web\Subdomains`

Why are subdomains important:

Development and Staging Environments: Companies often use subdomains to test new features or updates before deploying them to the main site. Due to relaxed security measures, these environments sometimes contain vulnerabilities or expose sensitive information.
Hidden Login Portals: Subdomains might host administrative panels or other login pages that are not meant to be publicly accessible. Attackers seeking unauthorised access can find these as attractive targets.
Legacy Applications: Older, forgotten web applications might reside on subdomains, potentially containing outdated software with known vulnerabilities.
Sensitive Information: Subdomains can inadvertently expose confidential documents, internal data, or configuration files that could be valuable to attackers.

subdomain bruteforcing tools:

dnsenum	- Comprehensive DNS enumeration tool that supports dictionary and brute-force attacks for discovering subdomains. (link: https://github.com/fwaeytens/dnsenum )
fierce	- User-friendly tool for recursive subdomain discovery, featuring wildcard detection and an easy-to-use interface. (link: https://github.com/mschwager/fierce)
dnsrecon	- Versatile tool that combines multiple DNS reconnaissance techniques and offers customisable output formats. (link: https://github.com/darkoperator/dnsrecon)
amass	- Actively maintained tool focused on subdomain discovery, known for its integration with other tools and extensive data sources. (link: https://github.com/owasp-amass/amass)
assetfinder	- Simple yet effective tool for finding subdomains using various techniques, ideal for quick and lightweight scans. (link: https://github.com/tomnomnom/assetfinder)
puredns	- Powerful and flexible DNS brute-forcing tool, capable of resolving and filtering results effectively. (link: https://github.com/d3mondev/puredns)

dnsenum
example usage on domain inlanefreight.com:
dnsenum --enum inlanefreight.com -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -r

-r: This option enables recursive subdomain brute-forcing, meaning that if dnsenum finds a subdomain, it will then try to enumerate subdomains of that subdomain.

**Powiązane:** [[Graph-Home]] [[OS-Unknown]]
