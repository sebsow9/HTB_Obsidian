---
Title: HTB\footprinting\SMTP
tags:
  - os/linux
  - os/network- cybersec
source: HTB\footprinting\SMTP
---

# HTB\footprinting\SMTP

> Źródło: `HTB\footprinting\SMTP`

SMTP
ports: 25, sometimes 587

Smtp works is unencrypted, thus SSL/TLS comes in play, than another port is used, for example 465

Default config

smtpd_banner = ESMTP Server
biff = no
append_dot_mydomain = no
readme_directory = no
compatibility_level = 2
smtp_tls_session_cache_database = btree:${data_directory}/smtp_scache
myhostname = mail1.inlanefreight.htb
alias_maps = hash:/etc/aliases
alias_database = hash:/etc/aliases
smtp_generic_maps = hash:/etc/postfix/generic
mydestination = $myhostname, localhost
masquerade_domains = $myhostname
mynetworks = 127.0.0.0/8 10.129.0.0/16
mailbox_size_limit = 0
recipient_delimiter = +
smtp_bind_address = 0.0.0.0
inet_protocols = ipv4
smtpd_helo_restrictions = reject_invalid_hostname
home_mailbox = /home/postfix

Commands for sending and communication

AUTH PLAIN	- AUTH is a service extension used to authenticate the client.
HELO	- The client logs in with its computer name and thus starts the session.
MAIL FROM	- The client names the email sender.
RCPT TO	- The client names the email recipient.
DATA	- The client initiates the transmission of the email.
RSET	- The client aborts the initiated transmission but keeps the connection between client and server.
VRFY	- The client checks if a mailbox is available for message transfer.
EXPN	- The client also checks if a mailbox is available for messaging with this command.
NOOP	- The client requests a response from the server to prevent disconnection due to time-out.
QUIT	- The client terminates the session.

Connection to SMTP 25 through telnet

telnet <ip address> <port>

SMTP errors and reply codes:
https://serversmtp.com/smtp-error/

VRFY - a command that might give away a username
VRFY <username>
if for example code 252 is returned, that confirms that no such user exists

Send an email:
"""
telnet 10.129.14.128 25

Trying 10.129.14.128...
Connected to 10.129.14.128.
Escape character is '^]'.
220 ESMTP Server

EHLO inlanefreight.htb

250-mail1.inlanefreight.htb
250-PIPELINING
250-SIZE 10240000
250-ETRN
250-ENHANCEDSTATUSCODES
250-8BITMIME
250-DSN
250-SMTPUTF8
250 CHUNKING

MAIL FROM: <cry0l1t3@inlanefreight.htb>

250 2.1.0 Ok

RCPT TO: <mrb3n@inlanefreight.htb> NOTIFY=success,failure

250 2.1.5 Ok

DATA

354 End data with <CR><LF>.<CR><LF>

From: <cry0l1t3@inlanefreight.htb>
To: <mrb3n@inlanefreight.htb>
Subject: DB
Date: Tue, 28 Sept 2021 16:32:51 +0200
Hey man, I am trying to access our XY-DB but the creds don't work.
Did you make any changes there?
.

250 2.0.0 Ok: queued as 6E1CF1681AB

QUIT

221 2.0.0 Bye
Connection closed by foreign host.
"""

The structure of an email header is defined by RFC5322
https://datatracker.ietf.org/doc/html/rfc5322

Open relay, if misconfigured may allow all ip addresses to not be filtered to spam, thus some servers, may have more priviliges than they should

Nmap smtp-commands script (is in default scripts -sC), which uses EHLO to list all possible commands that can be executed on the target smtp server

Nmap smtp-open-relay NSE script (--script) is used to identfiy the target SMTP server as an open realy

**Powiązane:** [[Graph-Home]] [[OS-Linux]] [[OS-Network]]
