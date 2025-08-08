---
Title: HTB\footprinting\Linux_protocols
tags:
  - os/linux
  - os/network- cybersec
source: HTB\footprinting\Linux_protocols
---

# HTB\footprinting\Linux_protocols

> Źródło: `HTB\footprinting\Linux_protocols`

SSH
TCP 22
It is an encrypted connection
SSH is natively implemented on all linux distribiutions and macOS.
Can be used on windows, bute requires additional programs to work.
There are two competing protoccols SSH-1 and SSH-2
SSH-2 is more advanced in ecnryption speed stability and security.
For example SSh-1 is vulnerable to MITM attacks.

OPENSSH has six different methods of authentication
1. Password
2. Public key
3. Host-based
4. Keyboard
5. Challenge response
6. GSSAPI

Other methods : https://www.golinuxcloud.com/openssh-authentication-methods-sshd-config/

Default config

sshd-congfig file is responsible for the OpenSSH server

Include /etc/ssh/sshd_config.d/*.conf
ChallengeResponseAuthentication no
UsePAM yes
X11Forwarding yes
PrintMotd no
AcceptEnv LANG LC_*
Subsystem       sftp    /usr/lib/openssh/sftp-server

Most of the settings are commented out and require manual configuration

Dangerous settings

PasswordAuthentication yes	- Allows password-based authentication.
PermitEmptyPasswords yes	- Allows the use of empty passwords.
PermitRootLogin yes	- Allows to log in as the root user.
Protocol 1	- Uses an outdated version of encryption.
X11Forwarding yes	- Allows X11 forwarding for GUI applications.
AllowTcpForwarding yes	- Allows forwarding of TCP ports.
PermitTunnel	- Allows tunneling.
DebianBanner yes	- Displays a specific banner when logging in.

ssh-audit
https://github.com/jtesta/ssh-audit
shows some gen eral information and which encryption algorithms are still used by the client

Shows what authentication methods are possible:
```bash
ssh -v cry0l1t3@10.129.14.132
```

Specyfying the method:
```bash
ssh -v cry0l1t3@10.129.14.132 -o PreferredAuthentications=password
```

RSYNC
Is a fast and efficient tool for locally and remotely copying files
Default port is 873, but can be configured to piggyback ssh connections
The guide to abuse it: https://book.hacktricks.xyz/network-services-pentesting/873-pentesting-rsync

We can connect to it using netcat, and check for shared folders:
```bash
nc -nv 127.0.0.1 873
```

(UNKNOWN) [127.0.0.1] 873 (rsync) open
@RSYNCD: 31.0
@RSYNCD: 31.0
#list
dev            	Dev Tools
@RSYNCD: EXIT

Or we can connect using rsync:
rsync -av --list-only rsync://127.0.0.1/dev

receiving incremental file list
drwxr-xr-x             48 2022/09/19 09:43:10 .
-rw-r--r--              0 2022/09/19 09:34:50 build.sh
-rw-r--r--              0 2022/09/19 09:36:02 secrets.yaml
drwx------             54 2022/09/19 09:43:10 .ssh

syncing files: rsync -av rsync://127.0.0.1/dev

rsync syntax over ssh https://phoenixnap.com/kb/how-to-rsync-over-ssh

R-services
usually ports 512,513,514
Are accessible through a suite of programs known as r-commands

The R-commands suite consists of the following programs https://en.wikipedia.org/wiki/Berkeley_r-commands
rcp
rexec
rlogin
rsh
rstat
ruptime
rwho

some R-commands that are abused:
rcp	- rshd	- 514	- TCP- 	Copy a file or directory bidirectionally from the local system to the remote system (or vice versa) or from one remote system to another. It works like the cp command on Linux but provides no warning to the user for overwriting existing files on a system.
rsh -	rshd -	514 - TCP	- Opens a shell on a remote machine without a login procedure. Relies upon the trusted entries in the /etc/hosts.equiv and .rhosts files for validation.
rexec - rexecd	- 512	- TCP	- Enables a user to run shell commands on a remote machine. Requires authentication through the use of a username and password through an unencrypted network socket. Authentication is overridden by the trusted entries in the /etc/hosts.equiv and .rhosts files.
rlogin	- rlogind	- 513	- TCP	- Enables a user to log in to a remote host over the network. It works similarly to telnet but can only connect to Unix-like hosts. Authentication is overridden by the trusted entries in the /etc/hosts.equiv and .rhosts files.

it is worth to check /etc/hosts.equiv

the /etc/hosts.equiv and .rhosts files on the system contain a list of ip addresses and hostnames that are trusted by the local host

This allows the user htb-student to log in from any ip
htb-student     10.0.17.5
+               10.0.17.10
+               +

as seen here
rlogin 10.0.17.2 -l htb-student

Last login: Fri Dec  2 16:11:21 from localhost

[htb-student@localhost ~]$

RWHO
rwho

root     web01:pts/0 Dec  2 21:34
htb-student     workstn01:tty1  Dec  2 19:57  2:25

RUSERS
rusers -al 10.0.17.5

htb-student     10.0.17.5:console          Dec 2 19:57     2:25

Final Thoughts
Remote management services can provide us with a treasure trove of data and often be abused for unauthorized access through either weak/default credentials or password re-use. We should always probe these services for as much information as we can gather and leave no stone unturned, especially when we have compiled a list of credentials from elsewhere in the target network.

**Powiązane:** [[Graph-Home]] [[OS-Linux]] [[OS-Network]]
