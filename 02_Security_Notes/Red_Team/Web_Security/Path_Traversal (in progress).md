---
title: Path Traversal
tags:
  - web_security
created: 2025-02-28
---

# Path_Traversal

## Description
`Path Traversal` also known as directory traversal, is a type of vulnerability that allows an attacker to access files that are stored outside the web application's root directory. By manipulating file paths, attackers can potentially `access sensitive files, configuration files, or other resources that should not be publicly accessible`.
## How to find it

## Common path traversal files to catch
-`/etc/passwd`

## Examples
- `https://example.com/getUserProfile.jsp?item=../../../../etc/passwd`
- 


## Fun Facts
- 

## Sources
- https://portswigger.net/web-security/file-path-traversal
- https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Directory%20Traversal
- https://infosecwriteups.com/finding-path-traversal-vulnerability-e2506d390569
