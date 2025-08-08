---
Title: HTB\footprinting\footprinting_medium
tags:
  - os/linux- cybersec
source: HTB\footprinting\footprinting_medium
---

# HTB\footprinting\footprinting_medium

> Źródło: `HTB\footprinting\footprinting_medium`

ip: 10.129.21.145

111/tcp  open  rpcbind
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
2049/tcp open  nfs
3389/tcp open  ms-wbt-server

alex:lol123!mD
sa:87N1ns@slls83

The answer was to connect to the RDP started on 3389
with the credentials found in tickets, while downloading an nfs share.
then when on windows, there was a file containing credentials to the admin.
On admin account, we could log onto sql database through SQL managment studio-> windows authentication
then we found users database, which could be viewed.
And it contained credentials for HTB account.

**Powiązane:** [[Graph-Home]] [[OS-Linux]]
