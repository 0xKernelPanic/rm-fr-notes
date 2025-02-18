# Footprinting

## **Enumeration Methodology**

Penetration testing and enumeration are dynamic processes. Our methodology uses 6 nested layers to systematically probe and pass through security boundaries. This structured approach, while adaptable to different environments, divides the enumeration process into three main levels:

| `Infrastructure-based enumeration` | `Host-based enumeration` | `OS-based enumeration` |
| --- | --- | --- |

![https://academy.hackthebox.com/storage/modules/112/enum-method3.png](https://academy.hackthebox.com/storage/modules/112/enum-method3.png)

| **Layer** | **Description** | **Information Categories** |
| --- | --- | --- |
| `1. Internet Presence` | Identification of internet presence and externally accessible infrastructure. | Domains, Subdomains, vHosts, ASN, Netblocks, IP Addresses, Cloud Instances, Security Measures |
| `2. Gateway` | Identify the possible security measures to protect the company's external and internal infrastructure. | Firewalls, DMZ, IPS/IDS, EDR, Proxies, NAC, Network Segmentation, VPN, Cloudflare |
| `3. Accessible Services` | Identify accessible interfaces and services that are hosted externally or internally. | Service Type, Functionality, Configuration, Port, Version, Interface |
| `4. Processes` | Identify the internal processes, sources, and destinations associated with the services. | PID, Processed Data, Tasks, Source, Destination |
| `5. Privileges` | Identification of the internal permissions and privileges to the accessible services. | Groups, Users, Permissions, Restrictions, Environment |
| `6. OS Setup` | Identification of the internal components and systems setup. | OS Type, Patch Level, Network config, OS Environment, Configuration files, sensitive private files |

## **Infrastructure Based Enumeration**

### **Domain Information**

- The first point of presence on the Internet may be the `SSL certificate`
 from the company's main website that we can examine. Often, such a 
certificate includes more than just a subdomain, and this means that the
 certificate is used for several domains, and these are most likely 
still active.
- Another source to find more subdomains is [crt.sh](https://crt.sh/). This source is [Certificate Transparency](https://en.wikipedia.org/wiki/Certificate_Transparency) logs.

```bash
curl -s https://crt.sh/\?q\=inlanefreight.com\&output\=json | jq .
[
  {
    "issuer_ca_id": 295815,
    "issuer_name": "C=US, O=Let's Encrypt, CN=R11",
    "common_name": "inlanefreight.com",
    "name_value": "inlanefreight.com\nwww.inlanefreight.com",
    "id": 16566286033,
    "entry_timestamp": "2025-01-11T14:08:25.84",
    "not_before": "2025-01-11T13:09:55",
    "not_after": "2025-04-11T13:09:54",
    "serial_number": "037d094e3320809c106cd897903a176e9a50",
    "result_count": 3
  }
  ]
```

```bash
#Output only subdomains
pr0xypirate@htb[/htb]$ curl -s https://crt.sh/\?q\=inlanefreight.com\&output\=json | jq . | grep name | cut -d":" -f2 | grep -v "CN=" | cut -d'"' -f2 | awk '{gsub(/\\n/,"\n");}1;' | sort -u

account.ttn.inlanefreight.com
blog.inlanefreight.com
bots.inlanefreight.com
console.ttn.inlanefreight.com
ct.inlanefreight.com
```

- [Shodan](https://www.shodan.io/) can be used to find devices and systems permanently connected to the Internet like `Internet of Things` (`IoT`).
 It searches the Internet for open TCP/IP ports and filters the systems 
according to specific terms and criteria. For example, open HTTP or 
HTTPS ports and other server ports for `FTP`, `SSH`, `SNMP`, `Telnet`, `RTSP`, or `SIP` are searched. As a result, we can find devices and systems, such as `surveillance cameras`, `servers`, `smart home systems`, `industrial controllers`, `traffic lights` and `traffic controllers`, and various network components.
    
    Useful comands to note are:
    
    ```bash
    curl -s https://crt.sh/\?q\=<target-domain>\&output\=json | jq . >> ip-addresses.txt
    for i in $(cat ip-addresses.txt);do shodan host $i;done
    ```
    

- DNS Records can be search by the following command

```bash
dig any inlanefreight.com
```

### **Cloud Resources**

Even though cloud providers secure their infrastructure centrally, this 
does not mean that companies are free from vulnerabilities. The 
configurations made by the administrators may nevertheless make the 
company's cloud resources vulnerable. This often starts with the `S3 buckets` (AWS), `blobs` (Azure), `cloud storage` (GCP), which can be accessed without authentication if configured incorrectly.

## Host Based Enumeration

[`FTP`](FTP%20193c0c6dba318031a19ad1e8363650cc.md)

[`SMB`](SMB%20194c0c6dba31809d9b2ed86d5ac1a139.md)

[`NFS`](NFS%20194c0c6dba318055ae58d6df9b1f7010.md)

[`DNS`](DNS%20195c0c6dba31808499f7f3e2e01a852e.md)

[`SMTP`](SMTP%20195c0c6dba3180c291b5d78a24146e80.md)

[IMAP / POP3](IMAP%20POP3%20196c0c6dba3180c6a69cd6f3656f7787.md)

[SNMP](SNMP%20196c0c6dba3180518986e14ba05f9d41.md)

[**MySQL**](MySQL%20197c0c6dba318034a41bdf937a0ba910.md)

[**MSSQL**](MSSQL%20197c0c6dba3180db9ae4f75d72113713.md)

[**Oracle TNS**](Oracle%20TNS%20198c0c6dba3180d6be43c7bc7e3a057b.md)

[**IPMI**](IPMI%2019ac0c6dba318029af17f6744d337950.md)

## **Remote Management Protocols**

### Linux

[**SSH**](SSH%2019cc0c6dba3180bdb5ebd0b1883ff43a.md)

[RSync](RSync%2019cc0c6dba3180a8b9edf00a1596895a.md)

### Windows

[**RDP**](RDP%2019cc0c6dba3180259b4ec5b703c8e1c9.md)

[WinRM](WinRM%2019dc0c6dba318073b60bddbd95e3e456.md)

## Labs

[labs_footprinting ](labs_footprinting%2019dc0c6dba31808c8a47f2327f3c7507.md)