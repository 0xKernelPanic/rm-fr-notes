---
title: SMB
tags:
  - protocol
  - networking
  - smb
  - file_sharing
  - reconnaissance
  - active
created: 2025-02-21
---

# SMB

## Basic Information
- **Full Name:**  Server Message Block
- **Abbreviation:** SMB
- **OSI Layer:** 7 (Application Layer)
- **Default Port(s):** 
	TCP 445 (Modern Implementations),
	TCP 137, 138, 139 (Older Implementations)
- **Protocol Type:** TCP

## Description
`SMB (Server Message Block)` is a network file-sharing protocol used to provide shared access to files, printers, and other network resources. It is primarily found in Windows environments but is also supported by Unix/Linux systems via `Samba`.

## Use Cases
- File and printer sharing in Windows networks
- Remote management of files and services

## Dangerous Settings

| **Setting**                 | **Description**                                                                   |
| --------------------------- | --------------------------------------------------------------------------------- |
| `browseable = yes`          | Allows listing available shares, making enumeration easier for attackers.         |
| `read only = no`            | Allows file modification, increasing risk of data tampering.                      |
| `writable = yes`            | Grants write permissions, which can be exploited for malicious purposes.          |
| `guest ok = yes`            | Allows unauthenticated access, posing a security risk.                            |
| `enable privileges = yes`   | Grants privilege escalation potential through SMB.                                |
| `create mask = 0777`        | Assigns full permissions to newly created files, increasing exposure.             |
| `directory mask = 0777`     | Assigns full permissions to directories, leading to possible unauthorized access. |
| `logon script = script.sh`  | Executes a script at login, potentially exploitable.                              |
| `magic script = script.sh`  | Runs a script when the session closes, which can be abused.                       |
| `magic output = script.out` | Stores script output, which may contain sensitive data.                           |

## Attack Vectors
1. EternalBlue allows remote code execution by exploiting vulnerabilities in SMBv1.
2. Null Session Authentication allows to list shares without credentials.
3. Privilege Escalation via misconfigured permissions.
4. Misconfigured shares with `writable = yes` can allow attackers to drop malicious payloads.

## Enumeration
#### Nmap Scan

```bash
nmap --script smb-enum-shares -p 139,445 <target_ip>
nmap --script smb-enum-users -p 139,445 <target_ip>
```

### SMB Client Enumeration
####  Null Session Authentication

`Null Session` occurs when you log in to a system with no username or password. This may reveal available shares on the system.
```bash
smbclient -N -L //<target_ip>
# More advanced that can do all the job
smbmap -H <target_ip>
crackmapexec smb <target_ip> --shares -u '' -p ''
samrdump.py <target_ip>
```

#### Connecting to a specific SMB share

```bash
smbclient //<target_ip>/<share> -U <username>
smbclient //<Ftarget_ip>/public -U "" # Anonymous login

```

### RPC Client Enumeration
`rpcclient` is used to gather  information about Windows environments. It allows for enumerating users, groups, and shares, querying system details, and interacting with Windows services remotely.

#### RPC Queries

| **Command**               | **Description**                                      |
| ------------------------- | ---------------------------------------------------- |
| `srvinfo`                 | Get basic server information.                        |
| `enumdomains`             | Enumerate available domains in the network.          |
| `querydominfo`            | Get domain, server, and user details.                |
| `netshareenumall`         | Enumerate all available shares.                      |
| `netsharegetinfo <share>` | Get information about a specific share.              |
| `enumdomusers`            | List all domain users.                               |
| `queryuser <RID>`         | Get user details based on RID (Relative Identifier). |
#### Enumerating domain users
Extract users from an SMB/RPC service:
```bash
rpcclient -U "" <target_ip> -c "enumdomusers"
```
This will return usernames and **RIDs**, which can be used to get more details.
#### Querying a specific user and group
Once you have a **RID** from `enumdomusers`, extract more info:
```bash
rpcclient -U "" <target_ip> -c "queryuser <user_RID>"
rpcclient -U "" <target_ip> -c "querygroup <group_RID>"
```

#### Brute-forcing users RIDs with samrdump

```bash
#https://github.com/fortra/impacket/blob/master/examples/samrdump.py
samrdump.py <target_ip>
```

### Enum4Linux-ng
This script performs extensive SMB and RPC enumeration on Windows and Samba systems.

```bash
git clone https://github.com/cddmp/enum4linux-ng.git
cd enum4linux-ng
chmod u+x enum4linux-ng
enum4linux -a <target_ip>
```

## Related

- **Samba** (smb for unix systems)
- **NFS** (Network File System)
- **RPC** (Remote Procedure Call)

## Fun Facts
- The infamous **WannaCry ransomware** exploited SMB vulnerabilities.
- Windows Server 2022 introduces SMB over QUIC, which enhances security by using TLS 1.3 encryption.

## References

- https://hackviser.com/tactics/pentesting/services/smb
- https://book.hacktricks.wiki/en/network-services-pentesting/pentesting-smb/index.html?highlight=smb#smb
