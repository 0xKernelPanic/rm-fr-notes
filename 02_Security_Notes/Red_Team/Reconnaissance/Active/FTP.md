---
title: FTP
tags:
  - protocol
  - networking
  - reconnaissance
  - active
  - file_sharing
created: 2025-02-18
---

# FTP

## Basic Information
- **Full Name:** File Transfer Protocol
- **Abbreviation:** FTP
- **OSI Layer:** Application (Layer 7)
- **Default Port(s):** 21
- **Protocol Type:** TCP

## Description
The File Transfer Protocol (FTP) is a standard network protocol used for the transfer of files between a client and server. It can be accessed through browsers, email clients, or dedicated FTP programs. 

FTP uses two channels: a control channel on `TCP port 21` for commands and status codes, and a data channel on `TCP port 20` for file transfers. If interrupted, transfers can resume automatically.

FTP operates in either `active` or `passive` mode. Active mode can be blocked by firewalls, so passive mode was developed to allow client-initiated connections through firewalls.

FTP typically requires credentials and transmits in `clear-text`. Some servers offer `anonymous FTP` access without passwords, though with limited functionality for security reasons.

#### Example FTP commands

| **Commands** | **Description**                       |
| ------------ | ------------------------------------- |
| `connect`    | Sets the remote host and port         |
| `get`        | Downloads files from remote host      |
| `put`        | Uploads files to remote host          |
| `quit`       | Exits tftp                            |
| `status`     | Shows current transfer settings       |
| `verbose`    | Toggles detailed transfer information |
## Use Cases
- Share files on servers
- Remote file access and management
- Automated file transfers

## Dangerous Settings

| **Setting**                    | **Description**                                                                     |
| ------------------------------ | ----------------------------------------------------------------------------------- |
| `anonymous_enable=YES`         | Allowing anonymous login?                                                           |
| `anon_upload_enable=YES`       | Allowing anonymous to upload files?                                                 |
| `anon_mkdir_write_enable=YES`  | Allowing anonymous to create new directories?                                       |
| `no_anon_password=YES`         | Do not ask anonymous for password?                                                  |
| `anon_root=/home/username/ftp` | Directory for anonymous.                                                            |
| `write_enable=YES`             | Allow the usage of FTP commands: STOR, DELE, RNFR, RNTO,  MKD, RMD, APPE, and SITE? |
## Attack Vectors
1. Weak Credentials
2. Anonymous Login
3. Known Vulnerabilities
4. FTP Bounce Attack

## Enumeration
#### Banner grabbing
```bash
nc -vn <target_ip> 21
openssl s_client -connect <target_domain>:21 -starttls ftp #Get certificate
```

#### Nmap scan
```bash
nmap -sV -sC -Pn -T4 -p21 <target_ip>
```

#### Download all available files
```bash
wget -m --no-passive ftp://anonymous:anonymous@<target_ip>:<port>
```

#### Browser connection
```
ftp://anonymous:anonymous@<target_ip>
```

## Related
- SFTP
- FTPS
- TFTP

## References
- https://hackviser.com/tactics/pentesting/services/ftp
- https://www.thehacker.recipes/infra/protocols/ftp
- https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-ftp/index.html?highlight=ftp#some-ftp-commands
- https://github.com/harsh-bothra/learn365/blob/main/days/day38.md
