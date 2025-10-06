---
title: ipcalc
tags:
  - command
  - ip
  - subnet
created: 2025-02-20
---

# ipcalc

## Description
`ipcalc`  takes  an  IPv4  address and netmask and calculates the resulting broadcast, network, Cisco wildcard mask, and host range. By giving a second netmask, you can design sub- and supernetworks.

## Commands

```bash
ipcalc <ip>/<netmask>
```

### Examples
```bash
ipcalc 192.168.0.1/24

ipcalc 192.168.0.1/255.255.128.0
```
