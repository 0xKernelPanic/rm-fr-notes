---
title: NFS
tags:
  - protocol
  - networking
  - active
  - file_sharing
  - reconnaissance
  - nfs
created: 2025-02-20
---

# NFS

## Basic Information
- **Full Name:** Network File System
- **Abbreviation:** NFS
- **OSI Layer:** Application (Layer 7)
- **Default Port(s):** 
	111 (RPCBind)
	2049 (NFS server daemon)
- **Protocol Type:** TCP or UDP (Depends on protocol version)

## Description
`NFS` is a distributed file system protocol that allows remote file access over a network as if they were local. It is commonly used in UNIX and Linux environments for sharing directories between systems. This means that NFS clients 
cannot communicate directly with SMB servers. NFS operates in a client-server model and uses Remote Procedure Calls (RPC) for communication.

### NFS Versions
| **Version** |                           **Key Features**                            |    **Transport Protocol**     |
| :---------: | :-------------------------------------------------------------------: | :---------------------------: |
|   `NFSv2`   |       Original version, limited features, no built-in security.       | UDP (default), TCP (optional) |
|   `NFSv3`   | Supports large files, asynchronous writes, and better error handling. |  UDP or TCP (both supported)  |
|   `NFSv4`   |     Includes authentication, encryption, and better performance.      |           TCP only            |

## Use Cases
- Used for centralized file storage and access across multiple clients.
- Enables shared media access across devices.

## Dangerous Settings
| **Option**        | **Description**                                                                                                      |
| ----------------- | -------------------------------------------------------------------------------------------------------------------- |
| `rw`              | Read and write permissions.                                                                                          |
| `insecure`        | Ports above 1024 will be used.                                                                                       |
| `nohide`          | If another file system was mounted below an exported directory, this directory is exported by its own exports entry. |
| `no_root_squash`  | All files created by root are kept with the UID/GID 0.                                                               |
| `anonuid/anongid` | Maps anonymous users to specific UID/GID, which can be misconfigured.                                                |

## Attack Vectors
1. Using `no_root_squash`, an attacker can gain root privileges remotely.
2. Attackers may place and execute malicious scripts in shared directories.
3. Weak NFS configurations allow unauthorized users access to files.

## Enumeration
#### Nmap scan
```bash
sudo nmap --script nfs* <target_ip> -sV -p111,2049
```

#### Show available NFS shares
```bash
showmount -e <target_ip>
```

#### Mounting an NFS share
```bash
mkdir target-NFS
sudo mount -t nfs <target_ip>:/ ./target-NFS/ -o nolock
cd target-NFS
tree .
```
#### File upload test
```bash
ls -la ./target-NFS #Checking permission on mounted share

# File upload and execution
echo "id" > ./target-NFS/test.sh
chmod +x ./target-NFS/test.sh
./target-NFS/test.sh
```

## Related
- **[[SMB (in progress)]]** Server Message Block 
- **RPC** (Remote Procedure Call)
- ***[[1 Projects/IT/02_Security_Notes/Red_Team/Reconnaissance/Active/FTP|FTP]]*** (File Transfer Protocol)

## References
- https://en.wikipedia.org/wiki/Network_File_System
- https://hackviser.com/tactics/pentesting/services/nfs
