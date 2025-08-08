---
Title: HTB\Password Attacks\Extracting Passwords from Windows
tags:
  - os/windows
  - os/linux- cybersec
source: HTB\Password Attacks\Extracting Passwords from Windows
---

# HTB\Password Attacks\Extracting Passwords from Windows

> Źródło: `HTB\Password Attacks\Extracting Passwords from Windows`

# Windows Client Auth Process
The Windows client authentication process(https://docs.microsoft.com/en-us/windows-server/security/windows-authentication/credentials-processes-in-windows-authentication)
involves multiple modules responsible for logon, credential retrieval, and verification.
Among the various authentication mechanisms in Windows, Kerberos is one of the most widely used and complex.
The Local Security Authority (LSA) is a protected subsystem that authenticates users,
manages local logins, oversees all aspects of local security, and provides services for translating between user names and security identifiers (SIDs).

Local interactive logon is handled through the coordination of several components:
the logon process (WinLogon: https://www.microsoftpressstore.com/articles/article.aspx?p=2228450&seqNum=8), the logon user interface process (LogonUI), credential providers,
the Local Security Authority Subsystem Service (LSASS), one or more authentication packages, and either the Security Accounts Manager (SAM) or Active Directory.
Authentication packages, in this context, are Dynamic-Link Libraries (DLLs) responsible for performing authentication checks.
For example, for non-domain-joined and interactive logins, the Msv1_0.dll authentication package is typically used.

## WinLogon
WinLogon is a trusted system process responsible for managing security-related user interactions, such as:
Launching LogonUI to prompt for credentials at login
Handling password changes
Locking and unlocking the workstation

To obtain a user's account name and password, WinLogon relies on credential providers installed on the system. These credential providers are COM objects implemented as DLLs.

WinLogon is the only process that intercepts login requests from the keyboard, which are sent via RPC messages from Win32k.sys.
At logon, it immediately launches the LogonUI application to present the graphical user interface.
Once the user's credentials are collected by the credential provider, WinLogon passes them to the Local Security Authority Subsystem Service (LSASS) to authenticate the user.

## LSASS
The Local Security Authority Subsystem Service (LSASS: https://en.wikipedia.org/wiki/Local_Security_Authority_Subsystem_Service)
is comprised of multiple modules and governs all authentication processes.
Located at %SystemRoot%\System32\Lsass.exein the file system, it is responsible for enforcing the local security policy, authenticating users,
and forwarding security audit logs to the Event Log. In essence, LSASS serves as the gatekeeper in Windows-based operating systems.
A more detailed illustration of the LSASS architecture can be found here:
https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-2000-server/cc961760(v=technet.10)?redirectedfrom=MSDN

Lsasrv.dll	The LSA Server service both enforces security policies and acts as the security package manager for the LSA. The LSA contains the Negotiate function, which selects either the NTLM or Kerberos protocol after determining which protocol is to be successful.
Msv1_0.dll	Authentication package for local machine logons that don't require custom authentication.
Samsrv.dll	The Security Accounts Manager (SAM) stores local security accounts, enforces locally stored policies, and supports APIs.
Kerberos.dll	Security package loaded by the LSA for Kerberos-based authentication on a machine.
Netlogon.dll	Network-based logon service.
Ntdsa.dll	This library is used to create new records and folders in the Windows registry.

## SAM database
The Security Account Manager (SAM) is a database file in Windows operating systems that stores user account credentials.
It is used to authenticate both local and remote users and uses cryptographic protections to prevent unauthorized access.
User passwords are stored as hashes in the registry, typically in the form of either LM or NTLM hashes.
The SAM file is located at %SystemRoot%\system32\config\SAM and is mounted under HKLM\SAM. Viewing or accessing this file requires SYSTEM level privileges.

Windows systems can be assigned to either a workgroup or domain during setup.
If the system has been assigned to a workgroup, it handles the SAM database locally and stores all existing users locally in this database.
However, if the system has been joined to a domain, the Domain Controller (DC) must validate the credentials from the Active Directory database (ntds.dit),
which is stored in %SystemRoot%\ntds.dit.

To improve protection against offline cracking of the SAM database, Microsoft introduced a feature in Windows NT 4.0 called SYSKEY (syskey.exe).
When enabled, SYSKEY partially encrypts the SAM file on disk, ensuring that password hashes for all local accounts are encrypted with a system-generated key.

## Credential Manager
Credential Manager is a built-in feature of all Windows operating systems that allows users to store and manage credentials used to access network resources, websites, and applications. These saved credentials are stored per user profile in the user's `Credential Locker`. The credentials are encrypted and stored at the following location:

```powershell-session
PS C:\Users\[Username]\AppData\Local\Microsoft\[Vault/Credentials]\
```

#### NTDS

It is very common to encounter network environments where Windows systems are joined to a Windows domain. This setup simplifies centralized management, allowing administrators to efficiently oversee all systems within their organization. In such environments, logon requests are sent to Domain Controllers within the same Active Directory forest. Each Domain Controller hosts a file called `NTDS.dit`, which is synchronized across all Domain Controllers, with the exception of [Read-Only Domain Controllers (RODCs)](https://docs.microsoft.com/en-us/windows/win32/ad/rodc-and-active-directory-schema).

`NTDS.dit` is a database file that stores Active Directory data, including but not limited to:

- User accounts (username & password hash)
- Group accounts
- Computer accounts
- Group policy objects

Later in this module, we will explore methods for extracting credentials from the `NTDS.dit` file.

Now that we have gone through a primer on credential storage concepts, let's study the various attacks we can perform to extract credentials and further our access during assessments.

# Attacking SAM, SYSTEM, and SECURITY






**Powiązane:** [[Graph-Home]] [[OS-Windows]] [[OS-Linux]]

