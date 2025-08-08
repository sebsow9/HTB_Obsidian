---
Title: HTB\shells&payloads\Live Assesment
tags:
  - os/linux
  - os/network- cybersec
source: HTB\shells&payloads\Live Assesment
---

# HTB\shells&payloads\Live Assesment

> Źródło: `HTB\shells&payloads\Live Assesment`

HOST 1
IP - 172.16.1.11
```bash
$nmap 172.16.1.11 -sC -sV
```

Starting Nmap 7.92 ( https://nmap.org ) at 2025-07-28 14:43 EDT
Nmap scan report for status.inlanefreight.local (172.16.1.11)
Host is up (0.024s latency).
Not shown: 989 closed tcp ports (conn-refused)
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
|_http-title: Inlanefreight Server Status
| http-methods:
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds  Windows Server 2019 Standard 17763 microsoft-ds
515/tcp  open  printer       Microsoft lpd
1801/tcp open  msmq?
2103/tcp open  msrpc         Microsoft Windows RPC
2105/tcp open  msrpc         Microsoft Windows RPC
2107/tcp open  msrpc         Microsoft Windows RPC
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info:
|   Target_Name: SHELLS-WINSVR
|   NetBIOS_Domain_Name: SHELLS-WINSVR
|   NetBIOS_Computer_Name: SHELLS-WINSVR
|   DNS_Domain_Name: shells-winsvr
|   DNS_Computer_Name: shells-winsvr
|   Product_Version: 10.0.17763
|_  System_Time: 2025-07-28T18:44:28+00:00
| ssl-cert: Subject: commonName=shells-winsvr
| Not valid before: 2025-07-27T18:37:58
|_Not valid after:  2026-01-26T18:37:58
|_ssl-date: 2025-07-28T18:44:34+00:00; +1s from scanner time.
8080/tcp open  http          Apache Tomcat 10.0.11
|_http-title: Apache Tomcat/10.0.11
|_http-open-proxy: Proxy might be redirecting requests
|_http-favicon: Apache Tomcat
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb-os-discovery:
|   OS: Windows Server 2019 Standard 17763 (Windows Server 2019 Standard 6.3)
|   Computer name: shells-winsvr
|   NetBIOS computer name: SHELLS-WINSVR\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2025-07-28T11:44:28-07:00
| smb-security-mode:
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-time:
|   date: 2025-07-28T18:44:28
|_  start_date: N/A
|_clock-skew: mean: 1h24m00s, deviation: 3h07m50s, median: 0s
| smb2-security-mode:
|   3.1.1:
|_    Message signing enabled but not required
|_nbstat: NetBIOS name: SHELLS-WINSVR, NetBIOS user: <unknown>, NetBIOS MAC: 00:50:56:94:6f:ed (VMware)

Found a vulnerable manager page in tomcat, that can be exploited using metasploit.
Used exploit - multi/http/tomcat_mgr_upload
It was importnat to change the default target to a windows machine, since we can see it is a Windows server 2019.
And adjust a compatible payload that can be executed on a windows machine.
Lastly to change the IP address since we want to connect our shell through an interface that is open to the network of the HOST-1.
Used payload - payload/windows/meterpreter/reverse_tcp

HOST-2 blog.inlanefreight.local
```bash
nmap does not show anything interesting.
```

There are credentials to the blog, and a prepared php shell in metasploit
After a configuration, MOST IMPORTANT WAS THE VHOST!!!!!

HOST-3 172.16.1.13
80/tcp  open  http         Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
|_http-title: 172.16.1.13 - /
| http-methods:
|_  Potentially risky methods: TRACE
135/tcp open  msrpc        Microsoft Windows RPC
139/tcp open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp open  microsoft-ds Windows Server 2016 Standard 14393 microsoft-ds
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 2h19m59s, deviation: 4h02m29s, median: 0s
|_nbstat: NetBIOS name: SHELLS-WINBLUE, NetBIOS user: <unknown>, NetBIOS MAC: 00:50:56:94:69:ea (VMware)
| smb2-time:
|   date: 2025-07-28T20:14:23
|_  start_date: 2025-07-28T18:37:47
| smb2-security-mode:
|   3.1.1:
|_    Message signing enabled but not required
| smb-security-mode:
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery:
|   OS: Windows Server 2016 Standard 14393 (Windows Server 2016 Standard 6.3)
|   Computer name: SHELLS-WINBLUE
|   NetBIOS computer name: SHELLS-WINBLUE\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2025-07-28T13:14:23-07:00

windows 2016 - vulnerable to eternal blue :P

**Powiązane:** [[Graph-Home]] [[OS-Linux]] [[OS-Network]]
