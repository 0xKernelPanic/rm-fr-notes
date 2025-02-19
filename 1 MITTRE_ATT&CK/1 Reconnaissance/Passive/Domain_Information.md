---
title: Domain_Information
tags:
  - reconnaissance
  - passive
  - fingerprinting
created: 2025-02-19
---

# Domain Information
There are several techniques used for web server and technology fingerprinting:
- **Banner Grabbing**: Analyzing service banners to detect software and version details.
- **HTTP Header Analysis**: Examining headers like `Server` and `X-Powered-By` to identify web server software and technologies.
- **Probing for Responses**: Sending crafted requests to trigger specific responses that reveal server details.
- **Page Content Analysis**: Inspecting page structure, scripts, or copyright headers for clues about underlying technologies.

A variety of tools exist that automate the fingerprinting process, combining various techniques to identify web servers, operating systems, content management systems, and other technologies:

| Tool         | Description                                                                                                           | Features                                                                                            |
| ------------ | --------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| `Wappalyzer` | Browser extension and online service for website technology profiling.                                                | Identifies a wide range of web technologies, including CMSs, frameworks, analytics tools, and more. |
| `BuiltWith`  | Web technology profiler that provides detailed reports on a website's technology stack.                               | Offers both free and paid plans with varying levels of detail.                                      |
| `WhatWeb`    | Command-line tool for website fingerprinting.                                                                         | Uses a vast database of signatures to identify various web technologies.                            |
| `Nmap`       | Versatile network scanner that can be used for various reconnaissance tasks, including service and OS fingerprinting. | Can be used with scripts (NSE) to perform more specialised fingerprinting.                          |
| `Netcraft`   | Offers a range of web security services, including website fingerprinting and security reporting.                     | Provides detailed reports on a website's technology, hosting provider, and security posture.        |
| `wafw00f`    | Command-line tool specifically designed for identifying Web Application Firewalls (WAFs).                             | Helps determine if a WAF is present and, if so, its type and configuration.                         |

## SSL Certificate
The SSL certificate of a website can provide valuable information about the domains and subdomains associated with it. Often, a single certificate can cover multiple domains.

## cURL
Use curl to grab server information
```bash
curl -I <domain>
```

## crt.sh
You can use **Certificate Transparency logs** to find subdomains
```bash
curl -s https://crt.sh/\?q\=inlanefreight.com\&output\=json | jq .
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

## Whois
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

## Shodan
Can be used to find devices and systems permanently connected to the Internet like `Internet of Things` (`IoT`). It searches the Internet for open TCP/IP ports and filters the systems according to specific terms and criteria. As a result, we can find devices and systems, such as `surveillance cameras`, `servers`, `smart home systems`, `industrial controllers`, `traffic lights` and `traffic controllers`, and various network components.
You can use the command below or check the website www.crt.sh
```bash
curl -s https://crt.sh/\?q\=<domain>\&output\=json | jq . >> ip_addresses.txt
```

Then:
```bash
for i in $(cat ip_addresses.txt);do shodan host $i;done
```

## Wafw00f
`Web Application Firewalls` (`WAFs`) are security solutions designed to protect web applications from various attacks. Before proceeding with further fingerprinting, it's crucial to determine if domain employs a WAF, as it could interfere with our probes or potentially block our requests.
```bash
wafw00f <domain>
```

## Nikto
`Nikto` is a powerful open-source web server scanner. In addition to its primary function as a vulnerability assessment tool, `Nikto's` fingerprinting capabilities provide insights into a website's technology stack. You can download it from https://github.com/sullo/nikto
```bash
nikto -h <domain> -Tuning b
```
In this command:
`-h` specifies target host
`-Tunning b` flag tells `nikto` to only run software identification


# 📡 Use Cases
- Basic information about the domain
- Company where the domain was registered
- Creation and expiration dates
- DNS Servers
- Technology and security information

# 📜 Sources
- https://who.is/

