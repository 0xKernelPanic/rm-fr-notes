# labs_footprinting

## **Footprinting Lab - Easy**

We were commissioned by the company `Inlanefreight Ltd` to test three different servers in their internal network. The company uses many different services, and the IT security department felt that a penetration test was necessary to gain insight into their overall security posture.

The first server is an internal DNS server that needs to be investigated. In particular, our client wants to know what information we can get out of these services and how this information could be used 
against its infrastructure. Our goal is to gather as much information as possible about the server and find ways to use that information against the company. `However, our client has made it clear that it is forbidden to attack the services aggressively using exploits, as these services 
are in production.` Additionally, our teammates have found the following credentials 
"`ceil:qwer1234`", and they pointed out that some of the company's 
employees were talking about SSH keys on a forum.

The administrators have stored a `flag.txt` file on this 
server to track our progress and measure success. Fully enumerate the 
target and submit the contents of this file as proof.

**IP Adress: 10.129.191.82**

### Nmap scan

```bash
PORT     STATE SERVICE VERSION
21/tcp   open  ftp
| fingerprint-strings:
|   GenericLines:
|     220 ProFTPD Server (ftp.int.inlanefreight.htb) [10.129.191.82]
|     Invalid command: try being more creative
|_    Invalid command: try being more creative
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   3072 3f:4c:8f:10:f1:ae:be:cd:31:24:7c:a1:4e:ab:84:6d (RSA)
|   256 7b:30:37:67:50:b9:ad:91:c0:8f:f7:02:78:3b:7c:02 (ECDSA)
|_  256 88:9e:0e:07:fe:ca:d0:5c:60:ab:cf:10:99:cd:6c:a7 (ED25519)
53/tcp   open  domain  ISC BIND 9.16.1 (Ubuntu Linux)
| dns-nsid:
|_  bind.version: 9.16.1-Ubuntu
2121/tcp open  ftp
| fingerprint-strings:
|   GenericLines:
|     220 ProFTPD Server (Ceil's FTP) [10.129.191.82]
|     Invalid command: try being more creative
|_    Invalid command: try being more creative
2 services unrecognized despite returning data. If you know the service/version, please submit the following fingerprints at https://nmap.org/cgi-bin/submit.cgi?new-service :
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port21-TCP:V=7.94SVN%I=7%D=2/16%Time=67B1EC72%P=x86_64-pc-linux-gnu%r(G
SF:enericLines,9C,"220\x20ProFTPD\x20Server\x20\(ftp\.int\.inlanefreight\.
SF:htb\)\x20\[10\.129\.191\.82\]\r\n500\x20Invalid\x20command:\x20try\x20b
SF:eing\x20more\x20creative\r\n500\x20Invalid\x20command:\x20try\x20being\
SF:x20more\x20creative\r\n");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port2121-TCP:V=7.94SVN%I=7%D=2/16%Time=67B1EC72%P=x86_64-pc-linux-gnu%r
SF:(GenericLines,8D,"220\x20ProFTPD\x20Server\x20\(Ceil's\x20FTP\)\x20\[10
SF:\.129\.191\.82\]\r\n500\x20Invalid\x20command:\x20try\x20being\x20more\
SF:x20creative\r\n500\x20Invalid\x20command:\x20try\x20being\x20more\x20cr
SF:eative\r\n");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

### Nmap UDP Scan

```bash
PORT   STATE SERVICE VERSION
53/udp open  domain  ISC BIND 9.16.1 (Ubuntu Linux)
| dns-nsid:
|_  bind.version: 9.16.1-Ubuntu
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

# We also discovered
Discovered open port 623/udp on 10.129.191.82
```

### SSH Login with cred

```bash
└──_ #ssh ceil@10.129.191.82
The authenticity of host '10.129.191.82 (10.129.191.82)' can't be established.
ED25519 key fingerprint is SHA256:AtNYHXCA7dVpi58LB+uuPe9xvc2lJwA6y7q82kZoBNM.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.129.191.82' (ED25519) to the list of known hosts.
ceil@10.129.191.82: Permission denied (publickey).
```

### FTP

```bash
wget -m --no-passive ftp://anonymous:anonymous@**10.129.191.82**
```

### DNS

```bash
[_]─[root@a1d7fec490e2]─[~]
└──_ #dig ns inlanefreight.htb @10.129.191.82
                                                                                                                        ; <<>> DiG 9.18.33-1~deb12u2-Debian <<>> ns inlanefreight.htb @10.129.191.82
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 48006
;; flags: qr aa rd; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 2
;; WARNING: recursion requested but not available
                                                                                                                        ;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
; COOKIE: 06ff4d533bf82c660100000067b1df8a27eabbd8701bcabb (good)
;; QUESTION SECTION:
;inlanefreight.htb.             IN      NS

;; ANSWER SECTION:
inlanefreight.htb.      604800  IN      NS      ns.inlanefreight.htb.

;; ADDITIONAL SECTION:
ns.inlanefreight.htb.   604800  IN      A       10.129.34.136

;; Query time: 60 msec
;; SERVER: 10.129.191.82#53(10.129.191.82) (UDP)
;; WHEN: Sun Feb 16 12:52:24 UTC 2025
;; MSG SIZE  rcvd: 107

┌─[root@a1d7fec490e2]─[~]
└──_ #
```

### DIG any query

```bash
└──_ #dig any inlanefreight.htb @10.129.191.82

; <<>> DiG 9.18.33-1~deb12u2-Debian <<>> any inlanefreight.htb @10.129.191.82                                                                                                                                    ;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 6995
;; flags: qr aa rd; QUERY: 1, ANSWER: 5, AUTHORITY: 0, ADDITIONAL: 2
;; WARNING: recursion requested but not available

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
; COOKIE: 607d6c22c2bfb3890100000067b1e05ad2fd1b79bc115a49 (good)                                                                                                                                                ;; QUESTION SECTION:
;inlanefreight.htb.             IN      ANY

;; ANSWER SECTION:
inlanefreight.htb.      604800  IN      TXT     "MS=ms97310371"                                                                                                                                                  inlanefreight.htb.      604800  IN      TXT     "v=spf1 include:mailgun.org include:_spf.google.com include:spf.protection.outlook.com include:_spf.atlassian.net ip4:10.129.124.8 ip4:10.129.127.2 ip4:10.129.42.106 ~all"
inlanefreight.htb.      604800  IN      TXT     "atlassian-domain-verification=t1rKCy68JFszSdCKVpw64A1QksWdXuYFUeSXKU"                                                                                           inlanefreight.htb.      604800  IN      SOA     inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800                                                                                         inlanefreight.htb.      604800  IN      NS      ns.inlanefreight.htb.

;; ADDITIONAL SECTION:
ns.inlanefreight.htb.   604800  IN      A       10.129.34.136

;; Query time: 60 msec
;; SERVER: 10.129.191.82#53(10.129.191.82) (TCP)
;; WHEN: Sun Feb 16 12:55:52 UTC 2025
;; MSG SIZE  rcvd: 437
```

### DIG AXFR Zone transfer

```bash
└──_ #dig axfr inlanefreight.htb @10.129.191.82
                                                                                                                                                                                                                 ; <<>> DiG 9.18.33-1~deb12u2-Debian <<>> axfr inlanefreight.htb @10.129.191.82
;; global options: +cmd
inlanefreight.htb.      604800  IN      SOA     inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
inlanefreight.htb.      604800  IN      TXT     "MS=ms97310371"
inlanefreight.htb.      604800  IN      TXT     "atlassian-domain-verification=t1rKCy68JFszSdCKVpw64A1QksWdXuYFUeSXKU"
inlanefreight.htb.      604800  IN      TXT     "v=spf1 include:mailgun.org include:_spf.google.com include:spf.protection.outlook.com include:_spf.atlassian.net ip4:10.129.124.8 ip4:10.129.127.2 ip4:10.129.42.106 ~all"
inlanefreight.htb.      604800  IN      NS      ns.inlanefreight.htb.
app.inlanefreight.htb.  604800  IN      A       10.129.18.15
internal.inlanefreight.htb. 604800 IN   A       10.129.1.6
mail1.inlanefreight.htb. 604800 IN      A       10.129.18.201
ns.inlanefreight.htb.   604800  IN      A       10.129.34.136
inlanefreight.htb.      604800  IN      SOA     inlanefreight.htb. root.inlanefreight.htb. 2 604800 86400 2419200 604800
;; Query time: 70 msec
;; SERVER: 10.129.191.82#53(10.129.191.82) (TCP)
;; WHEN: Sun Feb 16 12:57:40 UTC 2025
;; XFR size: 10 records (messages 1, bytes 540)
```

We can ping mail1.inlanefreight.htb. 604800 IN      A       10.129.18.201

### **IPMI**

### Nmap

```bash
PORT    STATE SERVICE
623/udp open  asf-rmcp
| ipmi-version:
|   Version:
|     IPMI-2.0
|   UserAuth: password, md5, md2, null
|   PassAuth: auth_msg, auth_user, non_null_user
|_  Level: 1.5, 2.0

```

```bash
[msf](Jobs:0 Agents:0) auxiliary(scanner/ipmi/ipmi_dumphashes) >> run'
[-] Parse error: Unmatched quote: "run'"
[msf](Jobs:0 Agents:0) auxiliary(scanner/ipmi/ipmi_dumphashes) >> run
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
[msf](Jobs:0 Agents:0) auxiliary(scanner/ipmi/ipmi_dumphashes) >> options

Module options (auxiliary/scanner/ipmi/ipmi_dumphashes):

   Name                  Current Setting                                                    Required  Description
   ----                  ---------------                                                    --------  -----------
   CRACK_COMMON          true                                                               yes       Automatically crack common passwords as they are obtained
   OUTPUT_HASHCAT_FILE                                                                      no        Save captured password hashes in hashcat format
   OUTPUT_JOHN_FILE                                                                         no        Save captured password hashes in john the ripper format
   PASS_FILE             /usr/share/metasploit-framework/data/wordlists/ipmi_passwords.txt  yes       File containing common passwords for offline cracking, one per line
   RHOSTS                10.129.191.82                                                      yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT                 623                                                                yes       The target port
   SESSION_MAX_ATTEMPTS  5                                                                  yes       Maximum number of session retries, required on certain BMCs (HP iLO 4, etc)
   SESSION_RETRY_DELAY   5                                                                  yes       Delay between session retries in seconds
   THREADS               1                                                                  yes       The number of concurrent threads (max one per host)
   USER_FILE             /usr/share/metasploit-framework/data/wordlists/ipmi_users.txt      yes       File containing usernames, one per line

View the full module info with the info, or info -d command.

[msf](Jobs:0 Agents:0) auxiliary(scanner/ipmi/ipmi_dumphashes) >> run
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
[msf](Jobs:0 Agents:0) auxiliary(scanner/ipmi/ipmi_dumphashes) >>
```

### SMTP on 10.129.18.201

### nmap scan 10.129.18.201

```bash
PORT     STATE SERVICE
25/tcp   open  smtp
110/tcp  open  pop3
143/tcp  open  imap
587/tcp  open  submission
1433/tcp open  ms-sql-s
3389/tcp open  ms-wbt-server
```

```bash
PORT     STATE SERVICE  VERSION
1433/tcp open  ms-sql-s Microsoft SQL Server 2019 15.00.2000.00; RTM
| ms-sql-ntlm-info:
|   10.129.18.201:1433:
|     Target_Name: WIN-02
|     NetBIOS_Domain_Name: WIN-02
|     NetBIOS_Computer_Name: WIN-02
|     DNS_Domain_Name: WIN-02
|     DNS_Computer_Name: WIN-02
|_    Product_Version: 10.0.17763
| ms-sql-info:
|   10.129.18.201:1433:
|     Version:
|       name: Microsoft SQL Server 2019 RTM
|       number: 15.00.2000.00
|       Product: Microsoft SQL Server 2019
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433
| ms-sql-tables:
|   10.129.18.201:1433:
|_[10.129.18.201:1433]
| ms-sql-empty-password:
|   10.129.18.201:1433:
|_    'sa' account password is not blank.
| ms-sql-dump-hashes:
|_  10.129.18.201:1433: ERROR: Bad username or password
| ms-sql-xp-cmdshell:
|_  (Use --script-args=ms-sql-xp-cmdshell.cmd='<CMD>' to change command.)                                                                                                                                        | ms-sql-config:
|   10.129.18.201:1433:
|_  ERROR: Bad username or password

```

## **Footprinting Lab - Medium**

This second server is a server that everyone on the internal network has access to. In our discussion with our client, we pointed out that these servers are often one of the main targets for attackers and that this server should be added to the scope.

Our customer agreed to this and added this server to our scope. Here, too, the goal remains the same. We need to find out as much information as possible about this server and find ways to use it against the server itself. For the proof and protection of customer data, a user named HTB has been created. Accordingly, we need to obtain the credentials of this user as proof.

### Nmap Scan

```bash
PORT      STATE SERVICE                                                                                                                                                                                          111/tcp   open  rpcbind                                                                                                                                                                                          | rpcinfo:                                                                                                                                                                                                       |   program version    port/proto  service
111/tcp   open  rpcbind     
| rpcinfo:               
|   program version    port/proto  service                                 
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/tcp6  rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  2,3,4        111/udp6  rpcbind                                                                                                                                                                       |   100003  2,3         2049/udp   nfs
|   100003  2,3         2049/udp6  nfs
|   100003  2,3,4       2049/tcp   nfs
|   100003  2,3,4       2049/tcp6  nfs                                                                                                                                                                           |   100005  1,2,3       2049/tcp   mountd
|   100005  1,2,3       2049/tcp6  mountd
|   100005  1,2,3       2049/udp   mountd
|   100005  1,2,3       2049/udp6  mountd                                                                                                                                                                        |   100021  1,2,3,4     2049/tcp   nlockmgr
|   100021  1,2,3,4     2049/tcp6  nlockmgr
|   100021  1,2,3,4     2049/udp   nlockmgr
|   100021  1,2,3,4     2049/udp6  nlockmgr                                                                                                                                                                      |   100024  1           2049/tcp   status
|   100024  1           2049/tcp6  status
|   100024  1           2049/udp   status
|_  100024  1           2049/udp6  status   
135/tcp   open  msrpc                                                                                                                                                                                            139/tcp   open  netbios-ssn                                                                                                                                                                                      445/tcp   open  microsoft-ds                                                                                                                                                                                     2049/tcp  open  nlockmgr
3389/tcp  open  ms-wbt-server
| ssl-cert: Subject: commonName=WINMEDIUM
| Issuer: commonName=WINMEDIUM
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-02-15T15:28:17
| Not valid after:  2025-08-17T15:28:17
| MD5:   1a3f:3179:1b76:6d27:fa69:bd80:a770:4eec
|_SHA-1: bcd4:6b21:12ca:38d4:7e4f:61ba:d72f:8ea2:dc90:a924
| rdp-ntlm-info:
|   Target_Name: WINMEDIUM
|   NetBIOS_Domain_Name: WINMEDIUM
|   NetBIOS_Computer_Name: WINMEDIUM
|   DNS_Domain_Name: WINMEDIUM
|   DNS_Computer_Name: WINMEDIUM
|   Product_Version: 10.0.17763
|_  System_Time: 2025-02-16T15:31:08+00:00
|_ssl-date: 2025-02-16T15:31:08+00:00; +2s from scanner time.     
5985/tcp  open  wsman                                                                                                                                                                                            47001/tcp open  winrm                                                                                                                                                                                            49664/tcp open  unknown                                                                                                                                                                                          
47001/tcp open  winrm

                                                                                                                                               5985/tcp  open  wsman                                                                                                                                                                                            47001/tcp open  winrm                                                                                                                                                                                            49664/tcp open  unknown                                                                                                                                                                                          49665/tcp open  unknown                                                                                                                                                                                          49666/tcp open  unknown                                                                                                                                                                                          49667/tcp open  unknown                                                                                                                                                                                          49668/tcp open  unknown                                                                                                                                                                                          49679/tcp open  unknown                                                                                                                                                                                          49680/tcp open  unknown                                                                                                                                                                                          49681/tcp open  unknown                                                                                                                                                                                                                                                                                                                                                                                                                                                   135/tcp   open  msrpc                                                                                                                                                                                            139/tcp   open  netbios-ssn                                                                                                                                                                                      445/tcp   open  microsoft-ds                                                                                                                                                                                     2049/tcp  open  nlockmgr
```

### NFS

```bash
└──╼ #showmount -e 10.129.222.3
Export list for 10.129.222.3:
/TechSupport (everyone)

```

```
┌─[root@parrot]─[/home/parrot/target-NFS/TechSupport]
└──╼ #cat ticket4238791283782.txt
Conversation with InlaneFreight Ltd

Started on November 10, 2021 at 01:27 PM London time GMT (GMT+0200)
---
01:27 PM | Operator: Hello,. 
 
So what brings you here today?
01:27 PM | alex: hello
01:27 PM | Operator: Hey alex!
01:27 PM | Operator: What do you need help with?
01:36 PM | alex: I run into an issue with the web config file on the system for the smtp server. do you mind to take a look at the config?
01:38 PM | Operator: Of course
01:42 PM | alex: here it is:

 1smtp {
 2    host=smtp.web.dev.inlanefreight.htb
 3    #port=25
 4    ssl=true
 5    user="alex"
 6    password="lol123!mD"
 7    from="alex.g@web.dev.inlanefreight.htb"
 8}
 9
10securesocial {
11    
12    onLoginGoTo=/
13    onLogoutGoTo=/login
14    ssl=false
15    
16    userpass {      
17    	withUserNameSupport=false
18    	sendWelcomeEmail=true
19    	enableGravatarSupport=true
20    	signupSkipLogin=true
21    	tokenDuration=60
22    	tokenDeleteInterval=5
23    	minimumPasswordLength=8
24    	enableTokenJob=true
25    	hasher=bcrypt
26	}
27
28     cookie {
29     #       name=id
30     #       path=/login
31     #       domain="10.129.2.59:9500"
32            httpOnly=true
33            makeTransient=false
34            absoluteTimeoutInMinutes=1440
35            idleTimeoutInMinutes=1440
36    }   

---

```

### Login via RDP

```
xfreerdp /u:alex /p:'lol123!mD' /v:10.129.222.3
```

### MSSQL sa user enabled

```
sa:87N1ns@slls83
```

## **Footprinting Lab - Hard**

### NMAP SCAN 10.129.51.12

```bash
┌─[root@a1d7fec490e2]─[~]
└──_ #cat nmap_output
# Nmap 7.94SVN scan initiated Mon Feb 17 14:27:25 2025 as: nmap -sC -sV -v -oN nmap_output 10.129.51.12
Nmap scan report for 10.129.51.12
Host is up (0.066s latency).
Not shown: 995 closed tcp ports (reset)
PORT    STATE SERVICE  VERSION
22/tcp  open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   3072 3f:4c:8f:10:f1:ae:be:cd:31:24:7c:a1:4e:ab:84:6d (RSA)
|   256 7b:30:37:67:50:b9:ad:91:c0:8f:f7:02:78:3b:7c:02 (ECDSA)
|_  256 88:9e:0e:07:fe:ca:d0:5c:60:ab:cf:10:99:cd:6c:a7 (ED25519)
110/tcp open  pop3     Dovecot pop3d
|_pop3-capabilities: CAPA RESP-CODES AUTH-RESP-CODE STLS UIDL TOP SASL(PLAIN) USER PIPELINING
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=NIXHARD
| Subject Alternative Name: DNS:NIXHARD
| Issuer: commonName=NIXHARD
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2021-11-10T01:30:25
| Not valid after:  2031-11-08T01:30:25
| MD5:   2b45:ec3c:508f:3cfb:9f6a:750c:63f8:2077
|_SHA-1: ed43:7d5a:3c46:54ac:9902:8dc4:9d86:6efb:2ae3:357c
143/tcp open  imap     Dovecot imapd (Ubuntu)
|_ssl-date: TLS randomness does not represent time
|_imap-capabilities: SASL-IR more have post-login Pre-login capabilities LITERAL+ IDLE listed OK IMAP4rev1 AUTH=PLAINA0001 STARTTLS LOGIN-REFERRALS ID ENABLE
| ssl-cert: Subject: commonName=NIXHARD
| Subject Alternative Name: DNS:NIXHARD
| Issuer: commonName=NIXHARD
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2021-11-10T01:30:25
| Not valid after:  2031-11-08T01:30:25
| MD5:   2b45:ec3c:508f:3cfb:9f6a:750c:63f8:2077
|_SHA-1: ed43:7d5a:3c46:54ac:9902:8dc4:9d86:6efb:2ae3:357c
993/tcp open  ssl/imap Dovecot imapd (Ubuntu)
| ssl-cert: Subject: commonName=NIXHARD
| Subject Alternative Name: DNS:NIXHARD
| Issuer: commonName=NIXHARD
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2021-11-10T01:30:25
| Not valid after:  2031-11-08T01:30:25
| MD5:   2b45:ec3c:508f:3cfb:9f6a:750c:63f8:2077
|_SHA-1: ed43:7d5a:3c46:54ac:9902:8dc4:9d86:6efb:2ae3:357c
|_imap-capabilities: SASL-IR more have post-login capabilities LITERAL+ IDLE listed OK IMAP4rev1 AUTH=PLAINA0001 Pre-login LOGIN-REFERRALS ID ENABLE
|_ssl-date: TLS randomness does not represent time
995/tcp open  ssl/pop3 Dovecot pop3d
|_ssl-date: TLS randomness does not represent time
|_pop3-capabilities: SASL(PLAIN) CAPA UIDL RESP-CODES TOP AUTH-RESP-CODE USER PIPELINING
| ssl-cert: Subject: commonName=NIXHARD
| Subject Alternative Name: DNS:NIXHARD
| Issuer: commonName=NIXHARD
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2021-11-10T01:30:25
| Not valid after:  2031-11-08T01:30:25
| MD5:   2b45:ec3c:508f:3cfb:9f6a:750c:63f8:2077
|_SHA-1: ed43:7d5a:3c46:54ac:9902:8dc4:9d86:6efb:2ae3:357c
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/bin/../share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Feb 17 14:27:46 2025 -- 1 IP address (1 host up) scanned in 21.63 seconds
```

### UDP SCAN

```bash

PORT    STATE SERVICE VERSION
161/udp open  snmp    net-snmp; net-snmp SNMPv3 server
| snmp-info:
|   enterprise: net-snmp
|   engineIDFormat: unknown
|   engineIDData: 5b99e75a10288b6100000000
|   snmpEngineBoots: 10
|_  snmpEngineTime: 54m46s

```

### SNMP

Community string is **backup**

```bash
┌─[root@a1d7fec490e2]─[~]
└──_ #onesixtyone -c snmp.txt 10.129.51.12
Scanning 1 hosts, 3219 communities
10.129.51.12 [backup] Linux NIXHARD 5.4.0-90-generic #101-Ubuntu SMP Fri Oct 15 20:00:55 UTC 2021 x86_64
```

```bash
┌─[_]─[root@a1d7fec490e2]─[~]                                                                                                                                              
└──_ #braa backup@10.129.51.12:.1.3.6.*      
```

We get                                                                                                                       

```
10.129.51.12:61ms:.0:Admin <tech@inlanefreight.htb>    
NIXHARD
nlanefreight
/opt/tom-recovery.sh         
tom:NMds732Js2761                                                                                                       
```

We can login to imap via tom and retrive emails

![image.png](image.png)

in mailbox we can see a tom’s private key

![image.png](image%201.png)

### SSH

```
┌─[root@a1d7fec490e2]─[~]
└──_ #ssh tom@10.129.51.12 -i tomprivkey.key

```

in tom’s bash history we can see that he login to mysql with user tom. password was the same

![image.png](image%202.png)