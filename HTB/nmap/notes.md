---
Title: HTB\nmap\notes
tags:
  - os/linux
  - os/network- cybersec
source: HTB\nmap\notes
---

# HTB\nmap\notes

> Źródło: `HTB\nmap\notes`

# scan types
- stealthy scan - SYN scan
- connect scan - nmap will try to connect to each port emulating somewhat of a human client, but still easily detectable

# scanned port types
State                 	Description
- open : This indicates that the connection to the scanned port has been established. These connections can be TCP connections, UDP datagrams as well as SCTP associations.
- closed : When the port is shown as closed, the TCP protocol indicates that the packet we received back contains an RST flag. This scanning method can also be used to determine if our target is alive or not.
- filtered : Nmap cannot correctly identify whether the scanned port is open or closed because either no response is returned from the target for the port or we get an error code from the target.
- unfiltered : This state of a port only occurs during the TCP-ACK scan and means that the port is accessible, but it cannot be determined whether it is open or closed.
- open|filtered	: If we do not get a response for a specific port, Nmap will set it to that state. This indicates that a firewall or packet filter may protect the port.
- closed|filtered :	This state only occurs in the IP ID idle scans and indicates that it was impossible to determine if the scanned port is closed or filtered by a firewall.

# links
- https://nmap.org/book/man-port-scanning-techniques.html - port scanning techniques

# saving
- to make an xml file an HTML file we can use: xsltproc <xml \file> -o <html \file>
- to view the html file use: firefox <file \name> or google-chrome <file\name>

# nmap scripting can be divided into:
- auth : Determination of authentication credentials.
- broadcast : Scripts, which are used for host discovery by broadcasting and the discovered hosts, can be automatically added to the remaining scans.
- brute : Executes scripts that try to log in to the respective service by brute-forcing with credentials.
- default :	Default scripts executed by using the -sC option.
- discovery :	Evaluation of accessible services.
- dos :	These scripts are used to check services for denial of service vulnerabilities and are used less as it harms the services.
- exploit :	This category of scripts tries to exploit known vulnerabilities for the scanned port.
- external : Scripts that use external services for further processing.
- fuzzer : This uses scripts to identify vulnerabilities and unexpected packet handling by sending different fields, which can take much time.
- intrusive : Intrusive scripts that could negatively affect the target system.
- malware :	Checks if some malware infects the target system.
- safe : Defensive scripts that do not perform intrusive and destructive access.
- version : Extension for service detection.
- vuln : Identification of specific vulnerabilities.
  (Scripts category) usage: nmap <target> --script <category>
  (specific scripts) usage:  nmap <target> --script <script-name>,<script-name>,...

# performance
-T 0 / -T paranoid
-T 1 / -T sneaky
-T 2 / -T polite
-T 3 / -T normal
-T 4 / -T aggressive
-T 5 / -T insane

each option is a pre-defined option for sending amounts of packets, the higher the value of T the more aggresive the scan becomes, meaning we are more likely to be seen, thus a hihger chance of being blocked by the security systems occur.

# for more information on nmap:
 https://nmap.org/nsedoc/index.html

10.129.2.80

**Powiązane:** [[Graph-Home]] [[OS-Linux]] [[OS-Network]]
