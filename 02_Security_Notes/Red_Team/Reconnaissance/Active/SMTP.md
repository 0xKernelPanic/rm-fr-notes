---
title: SMTP
tags:
  - protocol
  - networking
  - mail
created: 2025-02-23
---

# SMTP

## Basic Information
- **Full Name:** Simple Mail Transfer Protocol
- **Abbreviation:** SMTP
- **OSI Layer:** Application (Layer 7)
- **Default Port(s):** 
	25 (unencrypted SMTP)
	465 (encrypted SMTPS)
- **Protocol Type:** TCP

## Description
`SMTP (Simple Mail Transfer Protocol)` is a protocol used for sending email messages between servers. It is widely used for mail transmission over the internet. Clients use SMTP to send emails to mail servers, while mail servers use it to relay messages between each other. SMTP does not handle email retrieval, which is managed by protocols like [[IMAP_POP3]].

### SMTP Commands
| **Command**  | **Description**                                                                                  |
| ------------ | ------------------------------------------------------------------------------------------------ |
| `AUTH PLAIN` | AUTH is a service extension used to authenticate the client.                                     |
| `HELO`       | The client logs in with its computer name and thus starts the session.                           |
| `MAIL FROM`  | The client names the email sender.                                                               |
| `RCPT TO`    | The client names the email recipient.                                                            |
| `DATA`       | The client initiates the transmission of the email.                                              |
| `RSET`       | The client aborts the initiated transmission but keeps the connection between client and server. |
| `VRFY`       | The client checks if a mailbox is available for message transfer.                                |
| `EXPN`       | The client also checks if a mailbox is available for messaging with this command.                |
| `NOOP`       | The client requests a response from the server to prevent disconnection due to time-out.         |
| `QUIT`       | The client terminates the session.                                                               |
#### Telnet - HELO/EHLO
To interact with the SMTP server, we can use the `telnet` tool to initialize a TCP connection with the SMTP server. The actual initialization of the session is done with the command mentioned above, `HELO` or `EHLO`

#### Telnet - VRFY
The command `VRFY` can be used to enumerate existing users on
 the system. However, this does not always work. Depending on how the 
SMTP server is configured, the SMTP server may issue `code 252` and confirm the existence of a user that does not exist on the system. 

## Use Cases
- Sending emails from a client to a mail server
- Relaying emails between mail servers

## Dangerous Settings
| **Setting**                | **Description**                                                                        |
| -------------------------- | -------------------------------------------------------------------------------------- |
| `Open Relay Configuration` | Allow unauthorized users to send emails through a server (eg. mynetworks = 0.0.0.0/0). |
| `No TLS encryption`        | Allow to spoof e-mails.                                                                |

## Attack Vectors
1. Email Spoofing
2. Man-in-the-Middle (MITM) Attack
3. Relay Exploitation
4. Brute Force Attack to gain SMTP credentials

## Enumeration
#### Banner Grabbing
```bash
nc -vn <target_ip> 25
```

#### Nmap scan
```bash
nmap -sV -sC <target_ip> -p25,465 --script smtp-*

nmap <target_ip> -p25 --script smtp-open-relay -v
```

### Testing SMTP Authentication
```bash
telnet <target_ip> 25
HELO example.com
MAIL FROM:<attacker@example.com>
RCPT TO:<target@example.com>
DATA
This is a test email.
.
QUIT
```

### SMTP Recipiants Enumeration
```bash
smtp-user-enum -M VRFY -U mailusers.txt -t <target_ip> -w 12 -v
```

## Related
- [[IMAP_POP3]]
- SMTPS
## Fun Facts
- 
## References
- link
