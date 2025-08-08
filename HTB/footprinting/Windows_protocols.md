---
Title: HTB\footprinting\Windows_protocols
tags:
  - os/linux
  - os/network- cybersec
source: HTB\footprinting\Windows_protocols
---

# HTB\footprinting\Windows_protocols

> Źródło: `HTB\footprinting\Windows_protocols`

Main componnets used for remote managment of WIndows and Windows servers are the following:
Remote Desktop Protocol RDP
Windows Remote Management WinRM
Windows Managment Instrumentation WMI

RDP
Protcol allows display and control commadns to be transmitted via the GUI encrypted over IP networks.
Typically utilizing TCP 3389
UDP 3389 can be used as well

RDP can sometimes use RDP security, that uses identity-providing certificates which are self-signed by default, so the attacker can forge them

Footprinting RDP with nmap:
```bash
nmap -sV -sC 10.129.201.248 -p3389 --script rdp*
```

Some EDR's can recognize the packets as NMAP and might cut us off, because we got recognized as attackers

RDP-sec-check.pl by cisco

Installing:

```bash
sudo cpan
```text
cpan[1]> install Encoding::BER

Running the script:
git clone https://github.com/CiscoCXSecurity/rdp-sec-check.git && cd rdp-sec-check
```bash
./rdp-sec-check.pl 10.129.201.248
```

We can use xfreerdp, rdesktop or Remmina and interact with GUI of the server accordingly:
xfreerdp /u:cry0l1t3 /p:"P455w0rd!" /v:10.129.201.248

After successful authentication, a new window will appear with access to the server's desktop to which we have connected.

WinRM uses SOAP (Simple object Access Protocol) to establish connections to remote hosts and to their applications.
Tcp ports 5985, 5986
with 5986 using https

Another compomnent of WinRM is WinRS which is Windows Remote Shell, which lets us execute arbitrary commands on the remote system.

To interact with WinRM we can use evil-winrm, a tool designed to interact with WinRM on linux
evil-winrm -i 10.129.201.248 -u Cry0l1t3 -p P455w0rD!

WMI is microsoft implementation and laso an extension of the Common Information model CIM.
WMI allows read and write access to almost all settings on windows systems.
TCP port 135

wmiexec.py can be used to interact with the WMI:
/usr/share/doc/python3-impacket/examples/wmiexec.py Cry0l1t3:"P455w0rD!"@10.129.201.248 "hostname"

**Powiązane:** [[Graph-Home]] [[OS-Linux]] [[OS-Network]]
