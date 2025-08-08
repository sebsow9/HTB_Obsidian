---
Title: HTB\footprinting\DNS
tags:
  - os/linux
  - os/network- cybersec
source: HTB\footprinting\DNS
---

# HTB\footprinting\DNS

> Źródło: `HTB\footprinting\DNS`

Types of DNS servers
DNS Root Server	- The root servers of the DNS are responsible for the top-level domains (TLD). As the last instance, they are only requested if the name server does not respond. Thus, a root server is a central interface between users and content on the Internet, as it links domain and IP address. The Internet Corporation for Assigned Names and Numbers (ICANN) coordinates the work of the root name servers. There are 13 such root servers around the globe.
Authoritative Nameserver	- Authoritative name servers hold authority for a particular zone. They only answer queries from their area of responsibility, and their information is binding. If an authoritative name server cannot answer a client's query, the root name server takes over at that point.
Non-authoritative Nameserver	- Non-authoritative name servers are not responsible for a particular DNS zone. Instead, they collect information on specific DNS zones themselves, which is done using recursive or iterative DNS querying.
Caching DNS Server	- Caching DNS servers cache information from other name servers for a specified period. The authoritative name server determines the duration of this storage.
Forwarding Server	 - Forwarding servers perform only one function: they forward DNS queries to another DNS server.
Resolver	- Resolvers are not authoritative DNS servers but perform name resolution locally in the computer or router.

DNS records used in DNS queries

A	- Returns an IPv4 address of the requested domain as a result.
AAAA	- Returns an IPv6 address of the requested domain.
MX	- Returns the responsible mail servers as a result.
NS	-  Returns the DNS servers (nameservers) of the domain.
TXT	- This record can contain various information. The all-rounder can be used, e.g., to validate the Google Search Console or validate SSL certificates. In addition, SPF and DMARC entries are set to validate mail traffic and protect it from spam.
CNAME	- This record serves as an alias for another domain name. If you want the domain www.hackthebox.eu to point to the same IP as hackthebox.eu, you would create an A record for hackthebox.eu and a CNAME record for www.hackthebox.eu.
PTR	- The PTR record works the other way around (reverse lookup). It converts IP addresses into valid domain names.
SOA	- Provides information about the corresponding DNS zone and email address of the administrative contact.

soa lookup
dig soa www.inlanefreight.com

three different types of config files that might correspond to dns configuration
1. local dns configuration files
2. zone files
3. reverse name resolution files

Bind9 is a common linux based distribution:
- named.conf.local
- named.conf.options
- named.conf.log

Dangerous settings

allow-query	- Defines which hosts are allowed to send requests to the DNS server.
allow-recursion	- Defines which hosts are allowed to send recursive requests to the DNS server.
allow-transfer	- Defines which hosts are allowed to receive zone transfers from the DNS server.
zone-statistics	- Collects statistical data of zones.

DIG @ dns query, to check for other dns servers
dig ns inlanefreight.htb @10.129.14.128

DIG version query
dig CH TXT version.bind 10.129.120.85

DIG ANY query
dig any inlanefreight.htb @10.129.14.128

DIG axfr
dig axfr inlanefreight.htb @10.129.14.128

subdomain bruteforcing using dns and dig
```bash
for sub in $(cat /opt/useful/seclists/Discovery/DNS/subdomains-top1million-110000.txt);do dig $sub.inlanefreight.htb @10.129.14.128 | grep -v ';\|SOA' | sed -r '/^\s*$/d' | grep $sub | tee -a subdomains.txt;done
```


subdomain bruteforcing using DNSenum - https://github.com/fwaeytens/dnsenum
```bash
dnsenum --dnsserver 10.129.14.128 --enum -p 0 -s 0 -o subdomains.txt -f /opt/useful/seclists/Discovery/DNS/subdomains-top1million-110000.txt inlanefreight.htb
```


**Powiązane:** [[Graph-Home]] [[OS-Linux]] [[OS-Network]]
