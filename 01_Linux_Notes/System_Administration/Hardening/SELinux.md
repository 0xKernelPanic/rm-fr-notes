---
title: SELinux
tags:
  - hardening
  - linux_security
  - permissions
  - mac
  - dac
  - selinux
created: 2025-02-20
---

# SELinux

## Description
`SELinux` (Security-Enhanced Linux) is a powerful Linux kernel security module that implements **mandatory access control (MAC)**. It enhances system security by enforcing **policies** that restrict how processes and users can interact with files, devices, and other system resources. 

SELinux was designed to enhance existing security solutions, not to replace them. For example, **discretionary access control (DAC)** is still applied, even if the system is using SELinux. If DAC denies access first, SELinux is then not used as the access was already blocked by another mechanism.
### How it works
When an application (the subject) tries to access something like a file (the object), SELinux first checks a cache (the AVC) to see if it already knows whether to allow it. If the decision isn’t cached, SELinux calls an internal part of the kernel that acts like a policy judge. It grabs the security labels for both the process and the file, checks them against the set rules in the SELinux policy, and then decides whether to let the action happen or block it. If the action is blocked, it logs an `avc: denied` message. After the decision, it caches the result to make future checks faster.

### SELinux Concepts
| SELinux Concept | Description                                                                                                                                                                                                                                                                  |
| :-------------: | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|     `Modes`     | SELinux operates in `disabled`, `permissive`, or `enforcing` mode.                                                                                                                                                                                                           |
|     `Label`     | A label is an SELinux security context assigned to objects (files, processes, and ports).                                                                                                                                                                                    |
|    `Policy`     | A policy in SELinux is a set of rules that define what is allowed and denied on a system. These rules control how processes, files, and users interact. Policies enforce Mandatory Access Control (MAC) by using labels assigned to objects (files, processes, ports, etc.). |
| `File Context`  | The expected/default label for a file as defined in the SELinux policy. If a file’s label doesn’t match its file context, SELinux might block access.                                                                                                                        |
|      `AVC`      | Access Vector Cache logs denials in SELinux.                                                                                                                                                                                                                                 |
|   `Booleans`    | Booleans are on/off settings for functions in SELinux.                                                                                                                                                                                                                       |

## Use Cases
- It helps reduce the attack surface by restricting an app to access what it needs only. For example the SMB daemon can’t access anything not labeled for SMB sharing.
- Mainly used in enterprise environment.

## Commands
### SELinux Mode
#### Check status
```bash
getenforce
```

#### Set status

To set SELinux mode temporarily use `setenforce` command.
```bash
setenforce <permissive or enforced>
```

You can also make changes permanently in the `/etc/selinux/config` file.
```config
SELINUX=<disabled/permissive/enabled>
```

> [!Warning]
> If you disable SELinux on your system and then enable it later, make sure that you relabel your system. When SELinux is disabled and you perform changes to your file system, the changes are not reflected in the context anymore


### SELinux Context

####  Context Structure

Each process and file in an SELinux-enabled system has a context label. The format is:
```java
SELinux_user:role:type:level
```

To check it on a **file** use `ls`  with `-Z` flag
```bash
ls -laZ /home/jollyroger/document.txt
```

For **processes** you  can use `ps -eZ` command
```bash
ps -eZ | grep sshd
```

#### Modifying context structure

##### For files
```bash

# To list file or directory context
ls -laZ 
# To list contexts of a http
semanage fcontext -l | grep httpd 
# Add context for a folder
semanage fcontext -a -t httpd_sys_content_t "/root/www/(.*)?"

# Relabel
restorecon -R -v /root/www
```
##### For network services

```bash
# To list context of 80 port
semanage port -l | grep 80 

# To add httpd context on port 82/TCP
semanage port -a -t http_port_t -p tcp 82

# To remove httpd context on port 82/TCP
semanage port -d -t http_port_t -p tcp 82 

```

### Booleans
####  For a list of booleans and explanation of each one
```bash
semanage boolean -l
```

#### To temporarily enable booolean
for example to temporarily enable `httpd_can_network_connect_db` which allows Apache HTTP Server scripts and modules to connect to database servers.
```bash
setsebool httpd_can_network_connect_db on
```

#### getsebool to verify if boolean is enabled
```
getsebool httpd_can_network_connect_db
```

#### To make changes persistent across reboots
```bash
setsebool -P httpd_can_network_connect_db on
```

### SELinux Troubleshooting

#### Ausearch
Use the `ausearch` utility to find any recent AVC messages and confirm that SELinux denies the action.
```bash
ausearch -m AVC,USER_AVC -ts recent
```

#### Journalctl
Use the `journalctl` utility to view more information about the AVC message
```bash
journalctl -t setroubleshoot --since= [time]
#journalctl -t setroubleshoot --since=14:20
```

#### Grep on log file
Use grep with `denied` on /var/log/audit
```bash
grep denied /var/log/audit/*
```

## Related
- [[Apparmor]]
- [[Permissions]]

## Sources
- https://documentation.suse.com/en-us/sle-micro/6.0/html/Micro-selinux/index.html
- https://www.redhat.com/en/topics/linux/what-is-selinux#how-does-it-work
- https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/7/html/selinux_users_and_administrators_guide/sect-security-enhanced_linux-working_with_selinux-booleans#sect-Security-Enhanced_Linux-Booleans-Configuring_Booleans
- 
