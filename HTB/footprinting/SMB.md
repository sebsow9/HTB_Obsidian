---
Title: HTB\footprinting\SMB
tags:
  - os/linux- cybersec
source: HTB\footprinting\SMB
---

# HTB\footprinting\SMB

> Źródło: `HTB\footprinting\SMB`

# SMB - Server message protocol
A protocol that regulates access to files and entire directories and other network resources such as printers, routers, or interfaces realesed for the network.
# Samba
Alternative implementation for Unix-based operating systems. Sometimes called SMB/CIFS
Used TCP ports: 137, 138, 139
CIFS: 445 exclusively

Connecting:
smbclient -N -L //10.129.14.128
to a share:
smbclient //10.129.14.128/notes

# Default configuration
[sharename] -	The name of the network share.
workgroup = WORKGROUP/DOMAIN -	Workgroup that will appear when clients query.
path = /path/here/ -	The directory to which user is to be given access.
server string = STRING -	The string that will show up when a connection is initiated.
unix password sync = yes -	Synchronize the UNIX password with the SMB password?
usershare allow guests = yes -	Allow non-authenticated users to access defined share?
map to guest = bad user - What to do when a user login request doesn't match a valid UNIX user?
browseable = yes -	Should this share be shown in the list of available shares?
guest ok = yes -	Allow connecting to the service without using a password?
read only = yes -	Allow users to read files only?
create mask = 0700 -	What permissions need to be set for newly created files?
### Subsection
browseable = yes	Allow listing available shares in the current share?
read only = no	Forbid the creation and modification of files?
writable = yes	Allow users to create and modify files?
guest ok = yes	Allow connecting to the service without using a password?
enable privileges = yes	Honor privileges assigned to specific SID?
create mask = 0777	What permissions must be assigned to the newly created files?
directory mask = 0777	What permissions must be assigned to the newly created directories?
logon script = script.sh	What script needs to be executed on the user's login?
magic script = script.sh	Which script should be executed when the script gets closed?
magic output = script.out	Where the output of the magic script needs to be stored?
# RPCclient
Uses a concept called Remote Procedure Call
rpcclient -U "" 10.129.14.128 - connecting to a host "-U" a user flag

### Commands
srvinfo	Server information.
enumdomains	Enumerate all domains that are deployed in the network.
querydominfo	Provides domain, server, and user information of deployed domains.
netshareenumall	Enumerates all available shares.
netsharegetinfo <share>	Provides information about a specific share.
enumdomusers	Enumerates all domain users.
queryuser <RID>	Provides information about a specific user.

Example command to brute force users:
for i in $(seq 500 1100);do rpcclient -N -U "" 10.129.14.128 -c "queryuser 0x$(printf '%x\n' $i)" | grep "User Name\|user_rid\|group_rid" && echo "";done

Impacket - Samrdump.py a python script for brute forcing samba users
samrdump.py 10.129.14.128

SMBmap
smbmap -H 10.129.14.128

CrackMapExec
crackmapexec smb 10.129.14.128 --shares -u '' -p ''

Enum4Linux
Installation-:
git clone https://github.com/cddmp/enum4linux-ng.git
cd enum4linux-ng
```bash
pip3 install -r requirements.txt
```

Usage:
```bash
./enum4linux-ng.py 10.129.14.128 -A
```

**Powiązane:** [[Graph-Home]] [[OS-Linux]]
