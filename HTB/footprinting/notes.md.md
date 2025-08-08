---
Title: HTB\footprinting\notes.md
tags:
  - os/windows
  - os/linux- cybersec
source: HTB\footprinting\notes.md
---

# HTB\footprinting\notes.md

> Źródło: `HTB\footprinting\notes.md`

1.	There is more than meets the eye. Consider all points of view.
2.	Distinguish between what we see and what we do not see.
3.	There are always ways to gain more information. Understand the target.

# dividing enumeration into categories:
1. Internet Presence -	Identification of internet presence and externally accessible infrastructure. -	Domains, Subdomains, vHosts, ASN, Netblocks, IP Addresses, Cloud Instances, Security Measures
2. Gateway - Identify the possible security measures to protect the company's external and internal infrastructure. -	Firewalls, DMZ, IPS/IDS, EDR, Proxies, NAC, Network Segmentation, VPN, Cloudflare
3. Accessible Services - Identify accessible interfaces and services that are hosted externally or internally. -	Service Type, Functionality, Configuration, Port, Version, Interface
4. Processes - Identify the internal processes, sources, and destinations associated with the services. -	PID, Processed Data, Tasks, Source, Destination
5. Privileges - Identification of the internal permissions and privileges to the accessible services. - Groups, Users, Permissions, Restrictions, Environment
6. OS Setup	- Identification of the internal components and systems setup. -	OS Type, Patch Level, Network config, OS Environment, Configuration files, sensitive private files

# Certficate search
https://crt.sh - useful for checking subdomains of the target

komenda do outputu subdomen
- curl -s https://crt.sh/\?q\=inlanefreight.com\&output\=json | jq . | grep name | cut -d":" -f2 | grep -v "CN=" | cut -d'"' -f2 | awk '{gsub(/\\n/,"\n");}1;' | sort -u

nastepnie check ip subdomen
- for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f1,4;done

# Shodan
sprawdzenie czy adresy IP sa w bazie danych
for i in $(cat ip-addresses.txt);do shodan host $i;done

# Dig
sprawdza rekordy DNS

dig any <adres_strony>

A records: We recognize the IP addresses that point to a specific (sub)domain through the A record. Here we only see one that we already know.

MX records: The mail server records show us which mail server is responsible for managing the emails for the company. Since this is handled by google in our case, we should note this and skip it for now.

NS records: These kinds of records show which name servers are used to resolve the FQDN to IP addresses. Most hosting providers use their own name servers, making it easier to identify the hosting provider.

TXT records: this type of record often contains verification keys for different third-party providers and other security aspects of DNS, such as SPF, DMARC, and DKIM, which are responsible for verifying and confirming the origin of the emails sent. Here we can already see some valuable information if we look closer at the results.

**Powiązane:** [[Graph-Home]] [[OS-Windows]] [[OS-Linux]]
