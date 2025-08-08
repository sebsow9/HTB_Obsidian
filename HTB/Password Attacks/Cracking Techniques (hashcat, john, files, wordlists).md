---
Title: HTB\Password Attacks\Cracking Techniques (hashcat, john, files, wordlists)
tags:
  - os/linux- cybersec
source: HTB\Password Attacks\Cracking Techniques (hashcat, john, files, wordlists)
---

# HTB\Password Attacks\Cracking Techniques (hashcat, john, files, wordlists)

> Źródło: `HTB\Password Attacks\Cracking Techniques (hashcat, john, files, wordlists)`

Using John The ripper

Single crack mode is a rule-based cracking technique that is most useful when targeting Linux credentials.
It generates password candidates based on the victim's username, home directory name, and GECOS values (full name, room number, phone number, etc.).
These strings are run against a large set of rules that apply common string modifications seen in passwords
(e.g. a user whose real name is Bob Smith might use Smith1 as their password).

Wordlist mode is used to crack passwords with a dictionary attack, meaning it attempts all passwords in a supplied wordlist against the password hash.
The basic syntax for the command is as follows:
Ovas420@htb[/htb]$ john --wordlist=<wordlist_file> <hash_file>

Incremental mode is a powerful, brute-force-style password cracking mode that generates candidate passwords based on a statistical model (Markov chains: https://en.wikipedia.org/wiki/Markov_chain)
It is designed to test all character combinations defined by a specific character set, prioritizing more likely passwords based on training data.
By default, JtR uses predefined incremental modes specified in its configuration file (john.conf), which define character sets and password lengths.
You can customize these or define your own to target passwords that use special characters or specific patterns.

Identifying hashes is a problem. We can try:
https://openwall.info/wiki/john/sample-hashes
https://pentestmonkey.net/cheat-sheet/john-the-ripper-hash-formats
Both sources list multiple example hashes as well as the corresponding JtR format
Another option is to use a tool like hashID
By adding the -j flag, hashID will, in addition to the hash format, list the corresponding JtR format:
Ovas420@htb[/htb]$ hashid -j 193069ceb0461e1d40d216e32c79c704

Analyzing '193069ceb0461e1d40d216e32c79c704'
[+] MD2 [JtR Format: md2]
[+] MD5 [JtR Format: raw-md5]
[+] MD4 [JtR Format: raw-md4]
[+] Double MD5
[+] LM [JtR Format: lm]
[+] RIPEMD-128 [JtR Format: ripemd-128]
[+] Haval-128 [JtR Format: haval-128-4]
[+] Tiger-128
[+] Skein-256(128)
[+] Skein-512(128)
[+] Lotus Notes/Domino 5 [JtR Format: lotus5]
[+] Skype
[+] Snefru-128 [JtR Format: snefru-128]
[+] NTLM [JtR Format: nt]
[+] Domain Cached Credentials [JtR Format: mscach]
[+] Domain Cached Credentials 2 [JtR Format: mscach2]
[+] DNSSEC(NSEC3)
Unfortunately, in our example it is still quite unclear what format the hash is in. And this sometimes will be the case.

It is also possible to crack password-protected or encrypted files with JtR.
Multiple "2john" tools come with JtR that can be used to process files and produce hashes compatible with JtR.
The generalized syntax for these tools is:
Ovas420@htb[/htb]$ <tool> <file_to_crack> > file.hash
pdf2john	Converts PDF documents for John
ssh2john	Converts SSH private keys for John
mscash2john	Converts MS Cash hashes for John
etc...

Cracking with Hashcat
The general syntax used to run hashcat is as follows:
Ovas420@htb[/htb]$ hashcat -a 0 -m 0 <hashes> [wordlist, rule, mask, ...]
HashID can be used to quickly identify the hashcat hash type by specifying the -m argument.

Dictionary attack (-a 0) is, as the name suggests, a dictionary attack.
The user provides password hashes and a wordlist as input, and Hashcat tests each word in the list as a potential password until the correct one is found or the list is exhausted.
A wordlist alone is often not enough to crack a password hash.
As was the case with JtR, rules can be used to perform specific modifications to passwords to generate even more guesses.
The rule files that come with hashcat are typically found under /usr/share/hashcat/rules:
 One ruleset we could try is best64.rule, which contains 64 standard password modifications—such as appending numbers or substituting characters with their "leet" equivalents.
To perform this kind of attack, we would append the -r <ruleset> option to the command, as shown below:
Ovas420@htb[/htb]$ hashcat -a 0 -m 0 1b0556a75770563578569ae21392630c /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule

Mask attack
Mask attack (-a 3) is a type of brute-force attack in which the keyspace is explicitly defined by the user.
For example, if we know that a password is eight characters long, rather than attempting every possible combination,
we might define a mask that tests combinations of six letters followed by two numbers.
A mask is defined by combining a sequence of symbols, each representing a built-in or custom character set. Hashcat includes several built-in character sets:

Symbol	Charset
?l	abcdefghijklmnopqrstuvwxyz
?u	ABCDEFGHIJKLMNOPQRSTUVWXYZ
?d	0123456789
?h	0123456789abcdef
?H	0123456789ABCDEF
?s	«space»!"#$%&'()*+,-./:;<=>?@[]^_`{
?a	?l?u?d?s
?b	0x00 - 0xff

Let's say that we specifically want to try passwords which start with an uppercase letter, continue with four lowercase letters, a digit, and then a symbol.
The resulting hashcat mask would be ?u?l?l?l?l?d?s.
Ovas420@htb[/htb]$ hashcat -a 3 -m 0 1e293d6912d074c0fd15844d803400dd '?u?l?l?l?l?d?s'

Custom Wordlists and Rules
Let's look at a simple example using a password list with only one entry.
Ovas420@htb[/htb]$ cat password.list
password

We can use Hashcat to combine lists of potential names and labels with specific mutation rules to create custom wordlists.
Hashcat uses a specific syntax to define characters, words, and their transformations.
The complete syntax is documented in the official Hashcat rule-based attack documentation, but the examples below are sufficient to understand how Hashcat mutates input words.

:	Do nothing
l	Lowercase all letters
u	Uppercase all letters
c	Capitalize the first letter and lowercase others
sXY	Replace all instances of X with Y
```bash
$!	Add the exclamation character at the end
```

Each rule is written on a new line and determines how a given word should be transformed. If we write the functions shown above into a file, it may look like this:
Ovas420@htb[/htb]$ cat custom.rule

:
c
so0
c so0
sa@
c sa@
c sa@ so0
```bash
$!
$! c
$! so0
$! sa@
$! c so0
$! c sa@
$! so0 sa@
$! c so0 sa@
```

```bash
$! c
$! so0
$! sa@
$! c so0
$! c sa@
$! so0 sa@
$! c so0 sa@
```

```bash
$! so0
$! sa@
$! c so0
$! c sa@
$! so0 sa@
$! c so0 sa@
```

```bash
$! sa@
$! c so0
$! c sa@
$! so0 sa@
$! c so0 sa@
```

```bash
$! c so0
$! c sa@
$! so0 sa@
$! c so0 sa@
```

```bash
$! c sa@
$! so0 sa@
$! c so0 sa@
```

```bash
$! so0 sa@
$! c so0 sa@
```

```bash
$! c so0 sa@
```

We can use the following command to apply the rules in custom.rule to each word in password.list and store the mutated results in mut_password.list.
Ovas420@htb[/htb]$ hashcat --force password.list -r custom.rule --stdout | sort -u > mut_password.list
In this case, the single input word will produce fifteen mutated variants.
Ovas420@htb[/htb]$ cat mut_password.list

password
Password
passw0rd
Passw0rd
p@ssword
P@ssword
P@ssw0rd
password!
Password!
passw0rd!
p@ssword!
Passw0rd!
P@ssword!
p@ssw0rd!
P@ssw0rd!

Hashcat and JtR both come with pre-built rule lists that can be used for password generation and cracking.
One of the most effective and widely used rulesets is best64.rule, which applies common transformations that frequently result in successful password guesses.
It is important to note that password cracking and the creation of custom wordlists are, in most cases, a guessing game.

We can use a tool called CeWL(https://github.com/digininja/CeWL) to scan potential words from a company's website and save them in a separate list.
We can then combine this list with the desired rules to create a customized password list—one that has a higher probability of containing the correct password for an employee.
Ovas420@htb[/htb]$ cewl https://www.inlanefreight.com -d 4 -m 6 --lowercase -w inlane.wordlist
Ovas420@htb[/htb]$ wc -l inlane.wordlist
326

He was born on August 5, 1998
He works at Nexura, Ltd.
The company's password policy requires passwords to be at least 12 characters long, to contain at least one uppercase letter, at least one lowercase letter, at least one symbol and at least one number
He lives in San Francisco, CA, USA
He has a pet cat named Bella
He has a wife named Maria
He has a son named Alex
He is a big fan of baseball

Cracking Encrypted Files
Hunting for Encrypted Files
Many different extensions correspond to encrypted files—a useful reference list can be found on FileInfo(https://fileinfo.com/filetypes/encoded).
As an example, consider this command we might use to locate commonly encrypted files on a Linux system:
for ext in $(echo ".xls .xls* .xltx .od* .doc .doc* .pdf .pot .pot* .pp*");do echo -e "\nFile extension: " $ext; find / -name *$ext 2>/dev/null | grep -v "lib\|fonts\|share\|core" ;done

Certain files, such as SSH keys, do not have standard file extension.
In cases like these, it may be possible to identify files by standard content such as header and footer values.
For example, SSH private keys always begin with -----BEGIN [...SNIP...] PRIVATE KEY-----.
We can use tools like grep to recursively search the file system for them during post-exploitation.
Ovas420@htb[/htb]$ grep -rnE '^\-{5}BEGIN [A-Z0-9]+ PRIVATE KEY\-{5}$' /* 2>/dev/null

Some SSH keys are encrypted with a passphrase. With older PEM formats, it was possible to tell if an SSH key is encrypted based on the header,
which contains the encryption method in use. Modern SSH keys, however, appear the same whether encrypted or not.

One way to tell whether an SSH key is encrypted or not, is to try reading the key with ssh-keygen.
Ovas420@htb[/htb]$ ssh-keygen -yf ~/.ssh/id_ed25519
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIpNefJd834VkD5iq+22Zh59Gzmmtzo6rAffCx2UtaS6

As shown below, attempting to read a password-protected SSH key will prompt the user for a passphrase:
Ovas420@htb[/htb]$ ssh-keygen -yf ~/.ssh/id_rsa
Enter passphrase for "/home/jsmith/.ssh/id_rsa":

Cracking encrypted ssh keys
As mentioned in a previous section, JtR has many different scripts for extracting hashes from files—which we can then proceed to crack.
We can find these scripts on our system using the following command:
Ovas420@htb[/htb]$ locate *2john*

For example, we could use the Python script ssh2john.py to acquire the corresponding hash for an encrypted SSH key, and then use JtR to try and crack it.
Ovas420@htb[/htb]$ ssh2john.py SSH.private > ssh.hash
Ovas420@htb[/htb]$ john --wordlist=rockyou.txt ssh.hash

Cracking password protected documents
Today, most reports, documentation, and information sheets are commonly distributed as Microsoft Office documents or PDFs.
John the Ripper (JtR) includes a Python script called office2john.py, which can be used to extract password hashes from all common Office document formats.
Ovas420@htb[/htb]$ office2john.py Protected.docx > protected-docx.hash
Ovas420@htb[/htb]$ john --wordlist=rockyou.txt protected-docx.hash
Ovas420@htb[/htb]$ john protected-docx.hash --show
Protected.docx:1234

The process for cracking PDF files is quite similar, as we simply swap out office2john.py for pdf2john.py.
Ovas420@htb[/htb]$ pdf2john.py PDF.pdf > pdf.hash
Ovas420@htb[/htb]$ john --wordlist=rockyou.txt pdf.hash
Ovas420@htb[/htb]$ john pdf.hash --show
PDF.pdf:1234

Cracking Protected Archives
Ovas420@htb[/htb]$ curl -s https://fileinfo.com/filetypes/compressed | html2text | awk '{print tolower($1)}' | grep "\." | tee -a compressed_ext.txt

zip files (use zip2john)

When cracking OpenSSL encrypted files, we may encounter various challenges, including numerous false positives or complete failure to identify the correct password.
To mitigate this, a more reliable approach is to use the openssl tool within a for loop that attempts to extract the contents directly,
succeeding only if the correct password is found.

The following one-liner may produce several GZIP-related error messages, which can be safely ignored.
If the correct password list is used, as in this example, we will see another file successfully extracted from the archive.

Ovas420@htb[/htb]$ for i in $(cat rockyou.txt);do openssl enc -aes-256-cbc -d -in GZIP.gzip -k $i 2>/dev/null| tar xz;done

gzip: stdin: not in gzip format
tar: Child returned status 1
tar: Error is not recoverable: exiting now

gzip: stdin: not in gzip format
tar: Child returned status 1
tar: Error is not recoverable: exiting now
<SNIP>

Once the for loop has finished, we can check the current directory for a newly extracted file.
Ovas420@htb[/htb]$ ls
customers.csv  GZIP.gzip  rockyou.txt

Cracking Bitlocker drives
To crack a BitLocker encrypted drive, we can use a script called bitlocker2john to four different hashes:
the first two correspond to the BitLocker password, while the latter two represent the recovery key.
Because the recovery key is very long and randomly generated, it is generally not practical to guess—unless partial knowledge is available.
Therefore, we will focus on cracking the password using the first hash ($bitlocker$0$...).

Ovas420@htb[/htb]$ bitlocker2john -i Backup.vhd > backup.hashes
Ovas420@htb[/htb]$ grep "bitlocker\$0" backup.hashes > backup.hash
Ovas420@htb[/htb]$ cat backup.hash
```bash
$bitlocker$0$16$02b329c0453b9273f2fc1b927443b5fe$1048576$12$00b0a67f961dd80103000000$60$d59f37e70696f7eab6b8f95ae93bd53f3f7067d5e33c0394b3d8e2d1fdb885cb86c1b978f6cc12ed26de0889cd2196b0510bbcd2a8c89187ba8ec54f
```

Once a hash is generated, either JtR or hashcat can be used to crack it. For this example, we will look at the procedure with hashcat.
The hashcat mode associated with the $bitlocker$0$... hash is -m 22100. We supply the hash, specify the wordlist, and define the hash mode.
Since this encryption uses strong AES encryption, cracking may take considerable time depending on hardware performance.

Ovas420@htb[/htb]$ hashcat -a 0 -m 22100 '$bitlocker$0$16$02b329c0453b9273f2...[SNIP]...2a8c89187ba8ec54f' /usr/share/wordlists/rockyou.txt

The easiest method for mounting a BitLocker-encrypted virtual drive on Windows is to double-click the .vhd file.
Since it is encrypted, Windows will initially show an error. After mounting, simply double-click the BitLocker volume to be prompted for the password.

It is also possible to mount BitLocker-encrypted drives in Linux (or macOS). To do this, we can use a tool called dislocker. First, we need to install the package using apt:
Ovas420@htb[/htb]$ sudo apt-get install dislocker

Next, we create two folders which we will use to mount the VHD.
Ovas420@htb[/htb]$ sudo mkdir -p /media/bitlocker
Ovas420@htb[/htb]$ sudo mkdir -p /media/bitlockermount

We then use losetup to configure the VHD as loop device, decrypt the drive using dislocker, and finally mount the decrypted volume:
Ovas420@htb[/htb]$ sudo losetup -f -P Backup.vhd
Ovas420@htb[/htb]$ sudo dislocker /dev/loop0p2 -u1234qwer -- /media/bitlocker
Ovas420@htb[/htb]$ sudo mount -o loop /media/bitlocker/dislocker-file /media/bitlockermount

If everything was done correctly, we can now browse the files:
Ovas420@htb[/htb]$ cd /media/bitlockermount/
Ovas420@htb[/htb]$ ls -la

Once we have analyzed the files on the mounted drive, we can unmount it using the following commands:
Ovas420@htb[/htb]$ sudo umount /media/bitlockermount
Ovas420@htb[/htb]$ sudo umount /media/bitlocker

francisco

**Powiązane:** [[Graph-Home]] [[OS-Linux]]
