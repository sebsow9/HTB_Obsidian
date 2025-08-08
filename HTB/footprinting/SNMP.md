---
Title: HTB\footprinting\SNMP
tags:
  - os/linux- cybersec
source: HTB\footprinting\SNMP
---

# HTB\footprinting\SNMP

> Źródło: `HTB\footprinting\SNMP`

SNMP was created to montior network devices. In addition, this protocol can also be used to handle configuration tasks and change settings remotely.
SNMAP-enabled devices include routers, switches, servers, IoT devices, and many other devices that can also be queried and controlled using this standard protocol.

UDP 161 - transmits control commands
UDP 162 - snmp traps

MIB
Managment Information Base
its a textfile where all queryable snmp objects are listed
mib files are written in ASN.1 based ASCII text format
they do not contain data, but explain where to find information and what it looks like, which return values for the specific OID

OID
It represents nothing else than a node in a tree
OID registry - https://www.alvestrand.no/objectid/

SNMPv1
is used for monitoring and network managment
has no built in authentication mechanism, and does not support encryption

SNMPv2
the version that still exists is v2c, the c means community-based SNMP
security string is transmitted via plain text, and has no built-in encryption

SNMPv3
Security has been increased, there is an authentication process and transmission is encrypted using a pre-shared key
Complexity is increased, as there is significantly more options than in v2c

Community strings
they can be seen as passwords

Default configuration
sitting in snmpd.conf
snmp manpage: http://www.net-snmp.org/docs/man/snmpd.conf.html

Dangerous settings
rwuser noauth	- Provides access to the full OID tree without authentication.
rwcommunity <community string> <IPv4 address>	- Provides access to the full OID tree regardless of where the requests were sent from.
rwcommunity6 <community string> <IPv6 address>	- Same access as with rwcommunity with the difference of using IPv6.

Footprinting the service
we can use snmpwalk, onesixtyone, braa

SNMPwalk - we can check some information, if it is misconfigured
snmpwalk -v2c -c public 10.129.14.128

When we do not know the community string
Onesixtyone
```bash
sudo apt install onesixtyone
```text
onesixtyone -c /opt/useful/seclists/Discovery/SNMP/snmp.txt 10.129.14.128

Once w know the community string, we can brute force individual OIDs
Braa
```bash
sudo apt install braa
```
braa <community string>@<IP>:.1.3.6.*   # Syntax
braa public@10.129.14.128:.1.3.6.*

**Powiązane:** [[Graph-Home]] [[OS-Linux]]
