---
title: DNS
tags:
  - protocol
  - networking
  - dns
  - passive
  - active
  - virtual_hosts
created: 2025-02-19
---

# DNS

# 📌 Basic Information
- **Full Name:** Domain Name System
- **Abbreviation:** DNS
- **OSI Layer:** 7
- **Default Port(s):** 53 (unencrypted queries), 443 (DNS over HTTPS - DoH), 853 (DNS over TLS - DoT)
- **Protocol Type:** UDP (for standard queries), TCP (for larger queries or responses)

# 🔎 Description
The **Domain Name System (DNS)** is a decentralized naming system that converts human-readable domain names (like `example.com`) into machine-readable IP addresses (such as `192.168.1.1`). It’s a vital part of the internet's infrastructure, enabling devices to locate each other and communicate.

## DNS Concepts
|DNS Concept|Description|Example|
|---|---|---|
|`Domain Name`|A human-readable label for a website or other internet resource.|`www.example.com`|
|`IP Address`|A unique numerical identifier assigned to each device connected to the internet.|`192.0.2.1`|
|`DNS Resolver`|A server that translates domain names into IP addresses.|Your ISP's DNS server or public resolvers like Google DNS (`8.8.8.8`)|
|`Root Name Server`|The top-level servers in the DNS hierarchy.|There are 13 root servers worldwide, named A-M: `a.root-servers.net`|
|`TLD Name Server`|Servers responsible for specific top-level domains (e.g., .com, .org).|[Verisign](https://en.wikipedia.org/wiki/Verisign) for `.com`, [PIR](https://en.wikipedia.org/wiki/Public_Interest_Registry) for `.org`|
|`Authoritative Name Server`|The server that holds the actual IP address for a domain.|Often managed by hosting providers or domain registrars.|
|`DNS Record Types`|Different types of information stored in DNS.|A, AAAA, CNAME, MX, NS, TXT, etc.|
## How it Works (Step by Step):
1. **You type a domain** (e.g., `example.com`) into your browser.
2. **Your computer checks its cache** to see if it already knows the IP address.
3. **If not found, it asks the DNS resolver** (usually your ISP’s or a public DNS like Google’s `8.8.8.8`).
4. **The resolver queries different DNS servers**:
    - **Root server** → Knows where to find `.com` domains.
    - **TLD (Top-Level Domain) server** → Knows where to find `example.com`.
    - **Authoritative DNS server** → Knows the exact IP for `example.com`.
5. **The resolver returns the IP address** to your computer.
6. **Your browser connects to the website** using that IP.
## The Hosts File
The `hosts` file is located in `C:\Windows\System32\drivers\etc\hosts` on Windows and in `/etc/hosts` on Linux and MacOS. Can be useful for development, troubleshooting or blocking websites. You can also define `virtual hosts to access them through the browser`, enabling easy testing and development of multiple local sites or services.
```txt
<IP Address>    <Hostname> [<Alias> ...]
127.0.0.1       mywebsite.local
0.0.0.0       unwanted-site.com
94.237.55.238 admin.inlanefreight.htb #Virtual host 
```
## DNS Records
Different `DNS records` are used for the DNS queries, 
which all have various tasks. Moreover, separate entries exist for 
different functions since we can set up mail servers and other servers 
for a domain.

| **DNS Record** | **Description**                                                                                               | **Zone File Example**                                                                          |
| -------------- | ------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| `A`            | Returns an IPv4 address of the requested domain as a result.                                                  | `www.example.com.` IN A `192.0.2.1`                                                            |
| `AAAA`         | Returns an IPv6 address of the requested domain.                                                              | `www.example.com.` IN AAAA `2001:db8:85a3::8a2e:370:7334`                                      |
| `MX`           | Returns the responsible mail servers as a result.                                                             | `example.com.` IN MX 10 `mail.example.com.`                                                    |
| `NS`           | Returns the DNS servers (nameservers) of the domain.                                                          | `example.com.` IN NS `ns1.example.com.`                                                        |
| `TXT`          | This record can contain various information. The all-rounder can be                                           | `example.com.` IN TXT `"v=spf1 mx -all"` (SPF record)                                          |
| `CNAME`        | This record serves as an alias for another domain name.                                                       | `blog.example.com.` IN CNAME `webserver.example.net.`                                          |
| `PTR`          | The PTR record works the other way around (reverse lookup). It converts IP addresses into valid domain names. | `1.2.0.192.in-addr.arpa.` IN PTR `www.example.com.`                                            |
| `SOA`          | Provides information about the corresponding DNS zone and email address of the administrative contact.        | `example.com.` IN SOA `ns1.example.com. admin.example.com. 2024060301 10800 3600 604800 86400` |

## DNS ZONE
In the `Domain Name System` (`DNS`), a `zone` is a distinct part of the domain namespace that a specific entity or administrator manages. Think of it as a virtual container for a set of domain names. For example, `example.com` and all its subdomains (like `mail.example.com` or `blog.example.com`) would typically belong to the same DNS zone.

The zone file, a text file residing on a DNS server, defines the resource records within this zone, providing crucial information for translating domain names into IP addresses.
**Example DNS Zone**
```dns-zone
$TTL 3600 ; Default Time-To-Live (1 hour)
@       IN SOA   ns1.example.com. admin.example.com. (
                2024060401 ; Serial number (YYYYMMDDNN)
                3600       ; Refresh interval
                900        ; Retry interval
                604800     ; Expire time
                86400 )    ; Minimum TTL

@       IN NS    ns1.example.com.
@       IN NS    ns2.example.com.
@       IN MX 10 mail.example.com.
www     IN A     192.0.2.1
mail    IN A     198.51.100.1
ftp     IN CNAME www.example.com.
```

# 📡 Use Cases
- Resolves human-readable domain names to IP addresses.

# 🔒 Security
## Dangerous Settings
There are many ways in which a DNS server can be attacked. For 
example, a list of vulnerabilities targeting the BIND9 server can be 
found at [CVEdetails](https://www.cvedetails.com/product/144/ISC-Bind.html?vendor_id=64). In addition, SecurityTrails provides a short [list](https://securitytrails.com/blog/most-popular-types-dns-attacks) of the most popular attacks on DNS servers. There are also misconfigurations

| **Option**        | **Description**                                                                |
| ----------------- | ------------------------------------------------------------------------------ |
| `allow-query`     | Defines which hosts are allowed to send requests to the DNS server.            |
| `allow-recursion` | Defines which hosts are allowed to send recursive requests to the DNS server.  |
| `allow-transfer`  | Defines which hosts are allowed to receive zone transfers from the DNS server. |
| `zone-statistics` | Collects statistical data of zones.                                            |

## Attack Vectors
1. Uncovering assets
2. Mapping network infrastructure

## Enumeration
**Nmap UDP Scan**
```bash
namp <IP> -sU -p53 -sV -sC
```

**Nslookup**
```bash
nslookup <domain>
```

**DIG**
The `dig` command (`Domain Information Groper`) is a versatile and powerful utility for querying DNS servers and retrieving various types of DNS records.
```bash
dig a <domain>
dig ns <domain> @<nameserver> #NS request to the specific nameserver
dig any <domain> @<nameserver> #ANY record request (can be ignored)
dig +trace <domain> #Show full path of dns resolution
dig +x <IP> #Performs reverse lookup
```

### Zone Transfer (AXFR)
Zone transfer allows DNS zone data to sync between servers over TCP port 53. It ensures consistency across name servers using the `rndc-key` for authentication. Misconfiguring `allow-transfer` (e.g., setting it to `any`) can expose internal IPs and hostnames, posing security risks.
**DIG AXFR**
```bash
dig axfr <domain> @<nameserver> # AXFR zone transfer request
dig axfr internal.<domain> @<nameserver> 
```

###  Subdomain Enumeration
**dnsenum**
Many different tools can be used for this, and most of them work in the same way. One of these tools is, for example [DNSenum](https://github.com/fwaeytens/dnsenum). Wordlists we can be used from [Seclist](https://github.com/danielmiessler/SecLists).

```bash
dnsenum --enum <domain> -f /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt -r
```
In this command:
`-r` recursively will try to enumerate subdomains of that subdomain.

We can also enumerate subdomains

```bash
dnsenum --dnsserver <nameserver> --enum -o subdomains.txt -f /opt/useful/SecLists/Discovery/DNS/fierce-hostlist.txt <subdomain>.<domain>
```
In this command:
`-o` specifies the output file

### Virtual Hosts
At the core of `virtual hosting` is the ability of web servers to distinguish between multiple websites or applications sharing the same IP address. This is achieved by leveraging the `HTTP Host` header, a piece of information included in every `HTTP` request sent by a web browser.
Virtual hosts can also be configured to use different domains, not just subdomains. 
**example apache config**
```apache.conf
# Example of name-based virtual host configuration in Apache
<VirtualHost *:80>
    ServerName www.example1.com
    DocumentRoot /var/www/example1
</VirtualHost>

<VirtualHost *:80>
    ServerName www.example2.org
    DocumentRoot /var/www/example2
</VirtualHost>

<VirtualHost *:80>
    ServerName www.another-example.net
    DocumentRoot /var/www/another-example
</VirtualHost>
```

**Types of Virtual Hosting**
- `Name-Based` uses the HTTP Host header to differentiate websites on the same IP. Cost-effective and widely supported but has limitations with SSL/TLS.
- `IP-Based` assigns a unique IP per site, providing better isolation and protocol compatibility but requires more IPs.
- `Port-Based` hosts sites on different ports of the same IP. Useful when IPs are limited but less user-friendly as it requires specifying ports in the URL.

**Virtual Host Enumeration** 
GoBuster
```bash
gobuster vhost -u <URL>:<port> -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt --append-domain
```
There are a couple of other arguments that are worth knowing:

- Consider using the `-t` flag to increase the number of threads for faster scanning.
- The `-k` flag can ignore SSL/TLS certificate errors.
- You can use the `-o` flag to save the output to a file for later analysis.

Wfuzz
```bash
wfuzz -H "Host: FUZZ.inlanefreight.htb" --hc 404 -H "User-Agent: PENTEST" -c -z file,/home/jollyroger/Downloads/subdomains-top1million-20000.txt "http://94.237.55.238:33222" > output.txt
```

**Resolving Virtual Hosts**
```bash
curl -H "Host: your-virtual-host.com" http://your-server-ip-or-domain/
```

you can also add entry in `/etc/hosts` file.

# 🔗 Related
- **DNSSEC** (DNS Security Extension)
- **DoH** (DNS over HTTPS)
- **DoT** (DNS over TLS)


# 🧠 Fun Facts
- "`IN`" stands for `internet`. It's a class field in DNS records that specifies the protocol family. There are other class values, but there are rarely used (e.g `CH` for Chaosnet, `HS` for Hesiod) 
# 📜 References
- https://kb.wedos.com/pl/dns-pl/teoria-dns/protokol-dns-pl/protokol-dns-komunikacja-przez-udp-i-tcp/
- https://www.thehacker.recipes/infra/protocols/dns
- https://www.thehacker.recipes/web/recon/virtual-host-fuzzing

