---
Title: HTB\footprinting\FTP
tags:
  - os/linux
  - os/network- cybersec
source: HTB\footprinting\FTP
---

# HTB\footprinting\FTP

> Źródło: `HTB\footprinting\FTP`

# FTP
- ultimate cmmand list: https://web.archive.org/web/20230326204635/https://www.smartfile.com/blog/the-ultimate-ftp-commands-list/
- ultimate status codes https://en.wikipedia.org/wiki/List_of_FTP_server_return_codes
# TFTP
- uses UDP a simpler version of FTP
- commands:
- connect	: Sets the remote host, and optionally the port, for file transfers.
- get :	Transfers a file or set of files from the remote host to the local host.
- put :	Transfers a file or set of files from the local host onto the remote host.
- quit :	Exits tftp.
- status :	Shows the current status of tftp, including the current transfer mode (ascii or binary), connection status, time-out value, and so on.
- verbose :	Turns verbose mode, which displays additional information during file transfer, on or off.

# vsFTPd
- configuration file (usually) /etc/vsftpd.conf
# config file:
- listen=NO -	Run from inetd or as a standalone daemon?
- listen_ipv6=YES -	Listen on IPv6 ?
- anonymous_enable=NO -	Enable Anonymous access?
- local_enable=YES -	Allow local users to login?
- dirmessage_enable=YES -	Display active directory messages when users go into certain directories?
- use_localtime=YES -	Use local time?
- xferlog_enable=YES -	Activate logging of uploads/downloads?
- connect_from_port_20=YES -	Connect from port 20?
- secure_chroot_dir=/var/run/vsftpd/empty -	Name of an empty directory
- pam_service_name=vsftpd -	This string is the name of the PAM service vsftpd will use.
- rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem -	The last three options specify the location of the RSA certificate to use for SSL encrypted connections.
- rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key
- ssl_enable=NO

- /etc/ftpusers - contains users that are NOT permitted to log in to the FTP service

# Few more settings
- dirmessage_enable=YES -	Show a message when they first enter a new directory?
- chown_uploads=YES -	Change ownership of anonymously uploaded files?
- chown_username=username -	User who is given ownership of anonymously uploaded files.
- local_enable=YES -	Enable local users to login?
- chroot_local_user=YES -	Place local users into their home directory?
- chroot_list_enable=YES -	Use a list of local users that will be placed in their home directory?

# Looking for NSE scripts from nmap
-  find / -type f -name ftp* 2>/dev/null | grep scripts

# Service interaction
- sometimes the ftp server runs with TLS/SSL encryption, for this we can use openssl:
- openssl s_client -connect 10.129.14.136:21 -starttls ftp
- it might give us a server certificate, which might have information like a hostname, email address or location of the server.
- nc -nv 10.129.14.136 21
- telnet 10.129.14.136 21
- ftp <adres>

**Powiązane:** [[Graph-Home]] [[OS-Linux]] [[OS-Network]]
