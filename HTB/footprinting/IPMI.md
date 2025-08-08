---
Title: HTB\footprinting\IPMI
tags:
  - os/linux
  - os/network- cybersec
source: HTB\footprinting\IPMI
---

# HTB\footprinting\IPMI

> Źródło: `HTB\footprinting\IPMI`

Intelligent Platform Managment Interface is a set of standarized specifications fro hardware-based host managment systems used for system managment and monitoring.
It acs as an autonomous subsystem and works independently of the host's BIOS, CPU, firmware, and underlying operating system.

623 UDP

Access to the BMS of impi is equivalent to access to the motherboard of the host, which allows crazy things, like reinstalling operating system

simple nmap scan to footprint the service using NSE script impi-version
```bash
sudo nmap -sU --script ipmi-version -p 623 ilo.inlanfreight.local
```

Metasploit scanner module- https://www.rapid7.com/db/modules/auxiliary/scanner/ipmi/ipmi_version/

Some default BMC's passwords
Dell iDRAC	- root :	calvin
HP iLO	- Administrator :	randomized 8-character string consisting of numbers and uppercase letters
Supermicro IPMI	- ADMIN :	ADMIN

Little flaw in IMPI, specifically in RAKP protocol: http://fish2.com/ipmi/remote-pw-cracking.html
Basically the protocol is kind enough to give a password hash for a specific user.
Which allows a brute force for any valid username.

For easy exploit of the IPMI 2.0 exploit, we can use Metasploit's module:
https://www.rapid7.com/db/modules/auxiliary/scanner/ipmi/ipmi_dumphashes/

**Powiązane:** [[Graph-Home]] [[OS-Linux]] [[OS-Network]]
