---
title: RDP
tags:
  - protocol
  - networking
  - remote_access
  - windows
created: 2025-02-23
---

# RDP

## Basic Information
- **Full Name:** Remote Desktop Protocol
- **Abbreviation:** RDP
- **OSI Layer:** Application (Layer 7)
- **Default Port(s):** 3389
- **Protocol Type:** Hybrid

## Description
`Remote Desktop Protocol (RDP) `is a proprietary protocol developed by Microsoft that allows remote access to a Windows-based system over a network. It **enables graphical interface transmission** and control of remote machines, commonly used for remote administration and support. RDP **encrypts communication using TLS** but has historically been vulnerable to various attacks due to weak default settings and improper configurations.

## Use Cases
- Remote administration of Windows servers and desktops
- Providing technical support for users
- Accessing enterprise workstations from external locations

## Dangerous Settings

| **Setting**                     | **Description**                                                                                       |
| ------------------------------- | ----------------------------------------------------------------------------------------------------- |
| `Open access from the internet` | Exposing RDP directly to the internet without VPN or proper filtering leads to increased attack risk. |

## Attack Vectors
1. Weak credentials

## Enumeration
#### Nmap scan
```bash
nmap -sV -sC <target_ip> -p3389 --script rdp-*
```
##### Avoid scan detection
> [!Warning]
Threat hunters and EDR solutions can detect default Nmap RDP probes. Use packet trace analysis for deeper inspection:


```bash
nmap -sV -sC <target_ip> -p3389 --packet-trace --disable-arp-ping -n
```

#### Security Configuration Check
Developed by Cisco CX Security can unauthentically identify the security settings of RDP servers based on the handshakes.
```bash
#sudo cpan

git clone https://github.com/CiscoCXSecurity/rdp-sec-check.git
./rdp-sec-check.pl <target_ip>
```

#### Secure Authentication & Connection
 We can connect to RDP servers on Linux using `xfreerdp`, `rdesktop`, or `Remmina` and interact with the GUI of the server accordingly.
 ```bash
 xfreerdp /u:<username> /p:<password> /v:<target_ip>
```

## Related
- [[SSH]]
- Telnet
## Fun Facts
- 
## References
- 
