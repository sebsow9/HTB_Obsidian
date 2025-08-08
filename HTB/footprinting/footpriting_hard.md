---
Title: HTB\footprinting\footpriting_hard
tags:
  - os/linux- cybersec
source: HTB\footprinting\footpriting_hard
---

# HTB\footprinting\footpriting_hard

> Źródło: `HTB\footprinting\footpriting_hard`

IP: 10.129.148.208

22/tcp  open  ssh
110/tcp open  pop3
143/tcp open  imap
993/tcp open  imaps
995/tcp open  pop3s

fast UDP scan - only open ports
161/udp   open   snmp  net-snmp; net-snmp SNMPv3 server

first we brute force with onesixtyone to optain the community string
the output tell us its "backup"
we then use snmp walk to check some entries and OID
we find something that looks like credentials for user tom:
tom : NMds732Js2761

We can successfuly log onto tom's account on imaps, where we can find an email.
In the INBOX, when we view it, we can find, his private ssh key.
Which we then use to access his workstation via ssh.

dovecot-uidlist
3 V1636509064 N2 G991ec2188b258b618c8a0000afbeafc6
1 W3661 :key

i will try sql, since there is an mysql history file on tom's linux account
turns out there is an sql localhost server on 127.0.0.1, we can log in using toms credentials
we find a table called users which has our user HTB and his password.

**Powiązane:** [[Graph-Home]] [[OS-Linux]]
