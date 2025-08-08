---
Title: HTB\footprinting\OracleTNS
tags:
  - os/windows
  - os/linux
  - os/network- cybersec
source: HTB\footprinting\OracleTNS
---

# HTB\footprinting\OracleTNS

> Źródło: `HTB\footprinting\OracleTNS`

Oracle Transparent Network Substrate server is a communication protocol that facilitates communication between Oracle databases and applications over networks.

TCP 1521 - by default

Default config
files: tnsnames.ora, listener.ora
typically located in $ORACLE_HOME/network/admin dircetory

Example: Oracle 9 has a default password "CHANGE_ON_INSTALL"
whereas oracle 10 has no default password set
Oracle DBSNMP also uses a default password 'dbsnmp'
Many organizations still use 'finger' service together with oracle, which can put the service at risk, and make it vulnerable

Each database or service has a unique entry in tnsnames.ora file.
The entry consists of a name for the service, the network location of the service and the database or service name that clients should use when connecting.

Example of a simple entry:
ORCL =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = 10.129.11.102)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = orcl)
    )
  )

The entries can have more informations such as authentication details, connection pooling settings, and load balancing config.

On the other hand the listener.ora file is a server-side config file
Example of such entry:
SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (SID_NAME = PDB1)
      (ORACLE_HOME = C:\oracle\product\19.0.0\dbhome_1)
      (GLOBAL_DBNAME = PDB1)
      (SID_DIRECTORY_LIST =
        (SID_DIRECTORY =
          (DIRECTORY_TYPE = TNS_ADMIN)
          (DIRECTORY = C:\oracle\product\19.0.0\dbhome_1\network\admin)
        )
      )
    )
  )

LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = orcl.inlanefreight.htb)(PORT = 1521))
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
    )
  )

ADR_BASE_LISTENER = C:\oracle

In short, the client-side Oracle Net Services software uses the tnsnames.ora file to resolve service names to network addresses, while the listener process uses the listener.ora file to determine the services it should listen to and the behavior of the listener.

Oracle databases can be protected by a PlsqlExclusionList
it needs to be placed in $ORACLE_HOME/sqldeveloper and it contains the names of PL/SQL packages or types that should be excluded from execution.

Settings from config files:
DESCRIPTION	- A descriptor that provides a name for the database and its connection type.
ADDRESS	- The network address of the database, which includes the hostname and port number.
PROTOCOL	- The network protocol used for communication with the server
PORT	- The port number used for communication with the server
CONNECT_DATA	- Specifies the attributes of the connection, such as the service name or SID, protocol, and database instance identifier.
INSTANCE_NAME	- The name of the database instance the client wants to connect.
SERVICE_NAME	- The name of the service that the client wants to connect to.
SERVER	- The type of server used for the database connection, such as dedicated or shared.
USER	- The username used to authenticate with the database server.
PASSWORD	- The password used to authenticate with the database server.
SECURITY	- The type of security for the connection.
VALIDATE_CERT	- Whether to validate the certificate using SSL/TLS.
SSL_VERSION	- The version of SSL/TLS to use for the connection.
CONNECT_TIMEOUT	- The time limit in seconds for the client to establish a connection to the database.
RECEIVE_TIMEOUT	- The time limit in seconds for the client to receive a response from the database.
SEND_TIMEOUT	- The time limit in seconds for the client to send a request to the database.
SQLNET.EXPIRE_TIME	- The time limit in seconds for the client to detect a connection has failed.
TRACE_LEVEL	- The level of tracing for the database connection.
TRACE_DIRECTORY	- The directory where the trace files are stored.
TRACE_FILE_NAME	- The name of the trace file.
LOG_FILE	- The file where the log information is stored.

Oracle-tools-setup.sh
#!/bin/bash

```bash
sudo apt-get install libaio1 python3-dev alien -y
```bash
git clone https://github.com/quentinhardy/odat.git
cd odat/
git submodule init
git submodule update
```bash
wget https://download.oracle.com/otn_software/linux/instantclient/2112000/instantclient-basic-linux.x64-21.12.0.0.0dbru.zip
```

unzip instantclient-basic-linux.x64-21.12.0.0.0dbru.zip
```bash
wget https://download.oracle.com/otn_software/linux/instantclient/2112000/instantclient-sqlplus-linux.x64-21.12.0.0.0dbru.zip
```

unzip instantclient-sqlplus-linux.x64-21.12.0.0.0dbru.zip
export LD_LIBRARY_PATH=instantclient_21_12:$LD_LIBRARY_PATH
export PATH=$LD_LIBRARY_PATH:$PATH
```bash
pip3 install cx_Oracle
```

```bash
sudo apt-get install python3-scapy -y
sudo pip3 install colorlog termcolor passlib python-libnmap
sudo apt-get install build-essential libgmp-dev -y
```bash
```bash
pip3 install pycryptodome
```

Its in case we need some packages and tools to enumarate and interact with TNS listener

Then we can test if the installation was successful by using
```bash
./odat.py -h
```text
ODAT - oracle database attacking tool is an open source pentesting tool written in python and designed to enumarate and exploit vuln, in oracle databases.

SID - system identfier is a unique name that identifies a particulalr database instance.
When a client is connecting to a database he needs to specify the SID, if the SID is not specified, the default one specified in the tnsnames.ora file is used.

To enumarate or guess SIDS we can use tools like nmap, hydra, odat, and others.

NMAP - SID bruteforcing
```bash
sudo nmap -p1521 -sV 10.129.204.235 --open --script oracle-sid-brute
```text
ODAT - it can enumarate oracle database service and gain a lot of information, thus it has many options, in this example 'all' is used
```bash
./odat.py all -s 10.129.204.235
```text
SQLPLUS - connecting to the database
sqlplus <login>/<password>@10.129.204.235/<database SID>

if
"sqlplus: error while loading shared libraries: libsqlplus.so: cannot open shared object file: No such file or directory"
encountered
execute this:
```bash
sudo sh -c "echo /usr/lib/oracle/12.2/client64/lib > /etc/ld.so.conf.d/oracle-instantclient.conf";sudo ldconfig
```sql
SQL plus commands: https://docs.oracle.com/cd/E11882_01/server.112/e41085/sqlqraa001.htm#SQLQR985

Sometimes the user we log in as, does not have enough priviliges to look at some specific interesting stuff.
thus sometimes, if misconfigured, the admin of the database called "sysdba", might have given some permissions to the user, or is used by the admin himself, than we can try:
sqlplus <login>/<password>@10.129.204.235/<database SID> as sysdba

Oracle RDBMS - extract password hashes
select name, password from sys.user$;
Checking priviliges:
select * from user_role_privs;
Checking available tables:
select table_name from all_tables;

We ight have an option to upload a webshell, but we need to know a path of the webserver running on the database.
some default paths are:
Linux	- /var/www/html
Windows	- C:\inetpub\wwwroot

First try with files that are not known or wont be detected by antivirus, like a default txt file

Example:
echo "Oracle File Upload Test" > testing.txt
```bash
./odat.py utlfile -s 10.129.204.235 -d <db SID> -U <login> -P <passwd> --sysdba --putFile C:\\inetpub\\wwwroot testing.txt ./testing.txt
```

testing if it worked:
```bash
curl -X GET http://10.129.204.235/testing.txt
```

**Powiązane:** [[Graph-Home]] [[OS-Windows]] [[OS-Linux]] [[OS-Network]]
