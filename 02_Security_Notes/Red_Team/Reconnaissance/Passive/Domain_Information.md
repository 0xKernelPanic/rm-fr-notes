---
title: Domain_Information
tags:
  - reconnaissance
  - passive
  - fingerprinting
  - robots
created: 2025-02-19
---

## Domain Information
`Fingerprinting` focuses on extracting technical details about the technologies powering a website or web application. Similar to how a fingerprint uniquely identifies a person, the digital signatures of web servers, `operating systems`, and `software components`can reveal critical information about a target's infrastructure and potential `security weaknesses`. 
## Use Cases
- Basic information about the domain
- Company where the domain was registered
- Creation and expiration dates
- DNS Servers
- Technology and security information

## Manual Fingerprinting

There are several techniques used for web server and technology fingerprinting:
- **Banner Grabbing**: Analyzing service banners to detect software and version details.
- **HTTP Header Analysis**: Examining headers like `Server` and `X-Powered-By` to identify web server software and technologies.
- **Probing for Responses**: Sending crafted requests to trigger specific responses that reveal server details.
- **Page Content Analysis**: Inspecting page structure, scripts, or copyright headers for clues about underlying technologies.

A variety of tools exist that automate the fingerprinting process, combining various techniques to identify web servers, operating systems, content management systems, and other technologies:

| Tool         | Description                                                                                       | Features                                                                                            |
| ------------ | ------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| `Wappalyzer` | Browser extension and online service for website technology profiling.                            | Identifies a wide range of web technologies, including CMSs, frameworks, analytics tools, and more. |
| `BuiltWith`  | Web technology profiler that provides detailed reports on a website's technology stack.           | Offers both free and paid plans with varying levels of detail.                                      |
| `WhatWeb`    | Command-line tool for website fingerprinting.                                                     | Uses a vast database of signatures to identify various web technologies.                            |
| `Netcraft`   | Offers a range of web security services, including website fingerprinting and security reporting. | Provides detailed reports on a website's technology, hosting provider, and security posture.        |

### SSL Certificate
The SSL certificate of a website can provide valuable information about the domains and subdomains associated with it. Often, a single certificate can cover multiple domains.

### cURL
Use curl to grab server information
```bash
curl -I <target_domain>

# For virtual host use below syntax
curl -I -H "Host:  <vhost_domain>" http://<target_ip>:<port>
```

### Crawling
Crawling, often called `spidering`, is the automated process of systematically browsing the World Wide Web (eg. [web archive](https://web.archive.org/)). 
Some of the popular automatic webcrawlers tools are **BurpSuite Spider**, **OWASP ZAP** and **Scrapy**
#### Robots.txt
robots.txt is a simple text file placed in the root directory of a website (e.g. `www.example.com/robots.txt`). It adheres to the Robots Exclusion Standard, guidelines for how web crawlers should behave when visiting a website. This file contains instructions in the form of "directives" that tell bots which parts of the website they can and cannot crawl.

```txt
User-agent: *
Disallow: /admin/
Disallow: /private/
Allow: /public/

User-agent: Googlebot
Crawl-delay: 10

Sitemap: https://www.example.com/sitemap.xml
```

#### Sitemap
sitemap provides the URL to an XML sitemap for more efficient crawling (e.g. `https://www.example.com/sitemap.xml`)

#### Well-known
This designated location, typically accessible via the `/.well-known/` path on a web server, centralizes a website's critical metadata, including configuration files and information related to its services, protocols, and security mechanisms

### crt.sh
You can use **Certificate Transparency logs** to find subdomains
```bash
curl -s https://crt.sh/\?q\=`<target_domain>`\&output\=json | jq .
```

The output can look like this:
```json
  [...]
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
  [...]
```

### Whois
`WHOIS` is a widely used query and response protocol designed to access databases that store information about registered internet resources. Primarily associated with domain names, WHOIS can also provide details about IP address blocks and autonomous systems.
The usage
```bash
whois github.com
```

The output can look like this:
```txt
   Domain Name: GITHUB.COM
   Registry Domain ID: 1264983250_DOMAIN_COM-VRSN
   Registrar WHOIS Server: whois.markmonitor.com
   Registrar URL: http://www.markmonitor.com
   Updated Date: 2024-09-07T09:16:32Z
   Creation Date: 2007-10-09T18:20:50Z
   Registry Expiry Date: 2026-10-09T18:20:50Z
   Registrar: MarkMonitor Inc.
   Registrar IANA ID: 292
   [...]
```

### Shodan
Can be used to find devices and systems permanently connected to the Internet like `Internet of Things` (`IoT`). It searches the Internet for open TCP/IP ports and filters the systems according to specific terms and criteria. As a result, we can find devices and systems, such as `surveillance cameras`, `servers`, `smart home systems`, `industrial controllers`, `traffic lights` and `traffic controllers`, and various network components.
You can use the command below or check the website www.crt.sh
```bash
curl -s https://crt.sh/\?q\=`<target_domain>`\&output\=json | jq . >> ip_addresses.txt
```

Then:
```bash
for i in $(cat ip_addresses.txt);do shodan host $i;done
```

### Wafw00f
`Web Application Firewalls` (`WAFs`) are security solutions designed to protect web applications from various attacks. Before proceeding with further fingerprinting, it's crucial to determine if domain employs a WAF, as it could interfere with our probes or potentially block our requests.
```bash
wafw00f <target_domain>
```

### Nikto
`Nikto` is a powerful open-source web server scanner. In addition to its primary function as a vulnerability assessment tool, `Nikto's` fingerprinting capabilities provide insights into a website's technology stack. You can download it from https://github.com/sullo/nikto
```bash
nikto -h <target_domain> -Tuning b
```
In this command:
`-h` specifies target host
`-Tunning b` flag tells `nikto` to only run software identification

## Automating Recon
We can fastforward the above process using frameworks for web reconnaissance like FinalRecon.
### FinalRecon
`FinalRecon` offers a wealth of recon information:

- `Header Information`: Reveals server details, technologies used, and potential security misconfigurations.
- `Whois Lookup`: Uncovers domain registration details, including registrant information and contact details.
- `SSL Certificate Information`: Examines the SSL/TLS certificate for validity, issuer, and other relevant details.
- `Crawler`:
    - HTML, CSS, JavaScript: Extracts links, resources, and potential vulnerabilities from these files.
    - Internal/External Links: Maps out the website's structure and identifies connections to other domains.
    - Images, robots.txt, sitemap.xml: Gathers information about allowed/disallowed crawling paths and website structure.
    - Links in JavaScript, Wayback Machine: Uncovers hidden links and historical website data.
- `DNS Enumeration`: Queries over 40 DNS record types, including DMARC records for email security assessment.
- `Subdomain Enumeration`: Leverages multiple data sources (crt.sh, AnubisDB, ThreatMiner, CertSpotter, Facebook API, VirusTotal API, Shodan API, BeVigil API) to discover subdomains.
- `Directory Enumeration`: Supports custom wordlists and file extensions to uncover hidden directories and files.
- `Wayback Machine`: Retrieves URLs from the last five years to analyse website changes and potential vulnerabilities.

#### Installation
```bash
git clone https://github.com/thewhiteh4t/FinalRecon.git
cd FinalRecon
pip3 install -r requirements.txt
chmod u+x ./finalrecon.py
./finalrecon.py --help

```

#### Other useful tools
- https://github.com/lanmaster53/recon-ng
- https://github.com/laramies/theHarvester
- https://github.com/smicallef/spiderfoot
- https://osintframework.com/
- https://github.com/projectdiscovery/katana



## Sources
- 

