---
Title: HTB\footprinting\footprinting_easy
tags:
  - os/linux- cybersec
source: HTB\footprinting\footprinting_easy
---

# HTB\footprinting\footprinting_easy

> Źródło: `HTB\footprinting\footprinting_easy`

credentials:
ceil:qwer1234

IP: 10.129.15.142

Direct comments to root@nixeasy

the solution was to download all the files from an ftp server launched on 2121,
which was ceils ftp server, it contained a hidden directory .ssh that contained ceils private key

then we can use ssh to login to his account using the private ssh key

**Powiązane:** [[Graph-Home]] [[OS-Linux]]
