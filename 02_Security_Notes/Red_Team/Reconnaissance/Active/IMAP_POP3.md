---
title: IMAP POP3
tags:
  - protocol
  - networking
  - reconnaissance
  - active
  - mail
created: 2025-02-20
---

# IMAP/POP3

## Basic Information
- **Full Name:** Internet Message Access Protocol / Post Office Protocol v3
- **Abbreviation:** IMAP / POP3
- **OSI Layer:** Application (Layer 7)
- **Default Port(s):** 
	IMAP: 143 (unencrypted), 993(SSL/TLS)
	POP3: 110 (unencrypted), 995(SSL/TLS)
- **Protocol Type:** TCP

## Description
`IMAP and POP3`are standard email retrieval protocols used to access messages from a mail server. IMAP allows emails to remain on the server and be accessed from multiple devices, while POP3 downloads emails to a local device and usually removes them from the server.

#### IMAP commands
| **Command**                     | **Description**                                                                                               |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------- |
| `1 LOGIN username password`     | User's login.                                                                                                 |
| `1 LIST "" *`                   | Lists all directories.                                                                                        |
| `1 CREATE "INBOX"`              | Creates a mailbox with a specified name.                                                                      |
| `1 DELETE "INBOX"`              | Deletes a mailbox.                                                                                            |
| `1 RENAME "ToRead" "Important"` | Renames a mailbox.                                                                                            |
| `1 LSUB "" *`                   | Returns a subset of names from the set of names that the User has declared as being `active` or `subscribed`. |
| `1 SELECT INBOX`                | Selects a mailbox so that messages in the mailbox can be accessed.                                            |
| `1 UNSELECT INBOX`              | Exits the selected mailbox.                                                                                   |
| `1 FETCH <ID> all`              | Retrieves data associated with a message in the mailbox.                                                      |
| `1 CLOSE`                       | Removes all messages with the `Deleted` flag set.                                                             |
| `1 LOGOUT`                      | Closes the connection with the IMAP server.                                                                   |
| `1 FETCH 1 BODY[1]`             | Retrives body of the message in the mailbox.                                                                  |
#### POP3 commands
| **Command**     | **Description**                                             |
| --------------- | ----------------------------------------------------------- |
| `USER username` | Identifies the user.                                        |
| `PASS password` | Authentication of the user using its password.              |
| `STAT`          | Requests the number of saved emails from the server.        |
| `LIST`          | Requests from the server the number and size of all emails. |
| `RETR id`       | Requests the server to deliver the requested email by ID.   |
| `DELE id`       | Requests the server to delete the requested email by ID.    |
| `CAPA`          | Requests the server to display the server capabilities.     |
| `RSET`          | Requests the server to reset the transmitted information.   |
| `QUIT`          | Closes the connection with the POP3 server.                 |

## Use Cases
- **IMAP:** Suitable for users who need email access across multiple devices. 
- **POP3:** Useful for those who prefer offline access and want to store emails locally.
- **Both:** Used in email clients such as Thunderbird, Outlook, and Apple Mail.
- **Enterprise Use:** Many businesses use IMAP for centralized email management.
- **Security Compliance:** Organizations may configure IMAP/POP3 to comply with data retention policies.

## Dangerous Settings

| **Setting**               | **Description**                                                                           |
| ------------------------- | ----------------------------------------------------------------------------------------- |
| `auth_debug`              | Enables all authentication debug logging.                                                 |
| `auth_debug_passwords`    | This setting adjusts log verbosity, the submitted passwords, and the scheme gets logged.  |
| `auth_verbose`            | Logs unsuccessful authentication attempts and their reasons.                              |
| `auth_verbose_passwords`  | Passwords used for authentication are logged and can also be truncated.                   |
| `auth_anonymous_username` | This specifies the username to be used when logging in with the ANONYMOUS SASL mechanism. |

## Attack Vectors
1. Password authentication brute-force
2. Without encryption, credentials and emails can be intercepted.
3. Debug logs may leak sensitive authentication details.

## Enumeration
#### Nmap scan
```bash
sudo nmap <target_ip> -sV -p110,143,993,995 -sC
```

#### cURL - unencrypted interaction
```bash
curl -k 'imaps://<target_IP>' --user user:password -v
```

#### OpenSSL - TLS encrypted interaction
```bash
openssl s_client -connect <target_ip>:imaps
openssl s_client -connect <target_ip>:pop3s
```


## Related
- [[SMTP]]

## References
- https://www.atmail.com/blog/imap-101-manual-imap-sessions/
