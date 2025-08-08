---
Title: HTB\footprinting\MySQL
tags:
  - os/linux
  - os/network- cybersec
source: HTB\footprinting\MySQL
---

# HTB\footprinting\MySQL

> Źródło: `HTB\footprinting\MySQL`

MySQL works according to the client-server principle, and consists of of a MySQL server and onre or more MySQL clients.
Server port: 3306

The data is stored in tables with differnet columns rows and data types. These databases are often stored in a single file with the file extension .sql

MySQL clients
They can retrieve and edit the data. One of the best database usage is CMS WordPress. Wordpress stores all created posts, usernames, and passwords in their own database.

MySQL Databases
A known combination of SQL applications is called LAMP
Linux, Apache, MySQL, PHP
or when using Nginx as LEMP

Default Config
config file: /etc/mysql/mysql.conf.d/mysqld.cnf

[client]
port		= 3306
socket		= /var/run/mysqld/mysqld.sock

[mysqld_safe]
pid-file	= /var/run/mysqld/mysqld.pid
socket		= /var/run/mysqld/mysqld.sock
nice		= 0

[mysqld]
skip-host-cache
skip-name-resolve
user		= mysql
pid-file	= /var/run/mysqld/mysqld.pid
socket		= /var/run/mysqld/mysqld.sock
port		= 3306
basedir		= /usr
datadir		= /var/lib/mysql
tmpdir		= /tmp
lc-messages-dir	= /usr/share/mysql
explicit_defaults_for_timestamp

symbolic-links=0

!includedir /etc/mysql/conf.d/

Dangerous settings
user	- Sets which user the MySQL service will run as.
password	- Sets the password for the MySQL user.
admin_address	- The IP address on which to listen for TCP/IP connections on the administrative network interface.
debug	- This variable indicates the current debugging settings
sql_warnings	- This variable controls whether single-row INSERT statements produce an information string if warnings occur.
secure_file_priv	- This variable is used to limit the effect of data import and export operations.

extendedn mysql NSE nmap scan
```bash
sudo nmap 10.129.14.128 -sV -sC -p3306 --script mysql*
```

interaction with the mysql server
mysql -u root -pP4SSw0rd -h 10.129.14.128

Useful commands, when footprinting mysql server
mysql -u <user> -p<password> -h <IP address>	- Connect to the MySQL server. There should not be a space between the '-p' flag, and the password.
show databases;	- Show all databases.
use <database>;	- Select one of the existing databases.
show tables;	- Show all available tables in the selected database.
show columns from <table>;	- Show all columns in the selected database.
select * from <table>;	- Show everything in the desired table.
select * from <table> where <column> = "<string>";	- Search for needed string in the desired table.

**Powiązane:** [[Graph-Home]] [[OS-Linux]] [[OS-Network]]
