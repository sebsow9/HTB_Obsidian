---
Title: HTB\footprinting\MSSQL
tags:
  - os/windows
  - os/linux
  - os/network- cybersec
source: HTB\footprinting\MSSQL
---

# HTB\footprinting\MSSQL

> Źródło: `HTB\footprinting\MSSQL`

MSSQL - microsoft sql
usual port - 1433

MSSQL clients
SQL server managment studio (SSMS) somes a feature with the MSSQL isntall package.
It is commonly installed on the server for initial config and long term managment.
SSMS can be installed on any system, meaning we can come across a vulnerable system with SSMS that has saved credentials.

Many other clients can be used to access a database running on MSSQL
- mssql-cli
- SQL server powershell
- HeidiSQL
- SQLPro
- Impacket's mssqlclient.py

Impacket seems to be the most common and useful for pentesters
To find if we have it installed we can use:
locate mssqlclient

MSSQL Databases
Default system databases:
master	- Tracks all system information for an SQL server instance
model	- Template database that acts as a structure for every new database created. Any setting changed in the model database will be reflected in any new database created after changes to the model database
msdb	- The SQL Server Agent uses this database to schedule jobs & alerts
tempdb	- Stores temporary objects
resource	- Read-only database containing system objects included with SQL server

default config
Initial installation will likely run as NT SERVICE\MSSQLSERVER
Connecting through client side is possible through Windows Authentication, and by default encryption is not enforced.

Dangerous settings

We may benefit from looking into following:
- MSSQL cleints not using encryption when connecting to MSSQL server
- The use of self signed certificates. It is possible to spoof a self-signed certificate.
- The use of named pipes (https://learn.microsoft.com/en-us/sql/tools/configuration-manager/named-pipes-properties?view=sql-server-ver15)
- weak and default sa credentials. Admins may forget to disable this account

default nmap scan for misconfigured MSSQL server with "sa" user enabled:
```bash
sudo nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=1433,mssql.username=sa,mssql.password=,mssql.instance-name=MSSQLSERVER -sV -p 1433 10.129.201.248
```

Metasploit scanner
auxiliary(scanner/mssql/mssql_ping)

Connecting with Mssqlclient.py
(If we know credentials)
```bash
python3 mssqlclient.py Administrator@10.129.201.248 -windows-auth
```

Commands
cheat sheet: https://learnsql.com/blog/sql-server-cheat-sheet/

**Powiązane:** [[Graph-Home]] [[OS-Windows]] [[OS-Linux]] [[OS-Network]]
