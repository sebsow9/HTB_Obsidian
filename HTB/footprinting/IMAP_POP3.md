---
Title: HTB\footprinting\IMAP_POP3
tags:
  - os/network- cybersec
source: HTB\footprinting\IMAP_POP3
---

# HTB\footprinting\IMAP_POP3

> Źródło: `HTB\footprinting\IMAP_POP3`

IMAP allows online managment of emails directly on the server, but allows synchronization of a local email client with the mailbox on a remote server.
POP3 on the other hand does not have the same functionality as IMAP it only provides listing retrieving and deleting emails as functions at the email server.

IMAP - port used 143 or alternatively 993
POP3 - port 110 and 995

IMAP commands

1 LOGIN - username password	User's login.
1 LIST "" *	- Lists all directories.
1 CREATE "INBOX"	- Creates a mailbox with a specified name.
1 DELETE "INBOX"	- Deletes a mailbox.
1 RENAME "ToRead" "Important"	- Renames a mailbox.
1 LSUB "" *	- Returns a subset of names from the set of names that the User has declared as being active or subscribed.
1 SELECT INBOX	- Selects a mailbox so that messages in the mailbox can be accessed.
1 UNSELECT INBOX	- Exits the selected mailbox.
1 FETCH <ID> all	- Retrieves data associated with a message in the mailbox.
1 FETCH <ID> BODY[TEXT] - Displays the text body of the email
1 CLOSE	- Removes all messages with the Deleted flag set.
1 LOGOUT	- Closes the connection with the IMAP server.

POP3 commands

USER - username	Identifies the user.
PASS - password	Authentication of the user using its password.
STAT	- Requests the number of saved emails from the server.
LIST	- Requests from the server the number and size of all emails.
RETR id	- Requests the server to deliver the requested email by ID.
DELE id	- Requests the server to delete the requested email by ID.
CAPA	- Requests the server to display the server capabilities.
RSET	- Requests the server to reset the transmitted information.
QUIT	- Closes the connection with the POP3 server.

Dangerous settings

auth_debug	- Enables all authentication debug logging.
auth_debug_passwords	- This setting adjusts log verbosity, the submitted passwords, and the scheme gets logged.
auth_verbose	- Logs unsuccessful authentication attempts and their reasons.
auth_verbose_passwords	- Passwords used for authentication are logged and can also be truncated.
auth_anonymous_username	- This specifies the username to be used when logging in with the ANONYMOUS SASL mechanism.

Logging to IMAP when knowing user credentials
```bash
curl -k 'imaps://10.129.14.128' --user cry0l1t3:1234 -v
```

"-v" is recommended, it turns on verbose mode, and things such as what ssl encryption algorithm is used or the version of the mail server and TLS used

To interact with IMAP or POP3 server over SSL we can use openssl as well as netcat

openssl s_client -connect 10.129.14.128:pop3s
openssl s_client -connect 10.129.14.128:imaps

**Powiązane:** [[Graph-Home]] [[OS-Network]]
