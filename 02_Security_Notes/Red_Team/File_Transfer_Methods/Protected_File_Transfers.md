---
title: Protected_File_Transfers
tags:
  - file_sharing
  - encryption
created: 2025-02-27
---

# Protected_File_Transfers

## Description
We often gain access to highly sensitive data such as **user lists**, **credentials** or **critical information**. It is essential to encrypt this data or use secure data transfer methods like **SSH**, **SFTP**, and **HTTPS**. However, in cases where these options are unavailable, alternative encryption methods should be used.

## File Encryption on Windows
Many different methods can be used to encrypt files and information on Windows systems. One of the simplest methods is the [Invoke-AESEncryption.ps1](https://www.powershellgallery.com/packages/DRTools/4.0.2.3/Content/Functions%5CInvoke-AESEncryption.ps1) PowerShell script. This script is small and provides encryption of files and strings.

```powershell
.EXAMPLE
Invoke-AESEncryption -Mode Encrypt -Key "p@ssw0rd" -Text "Secret Text" 

Description
-----------
Encrypts the string "Secret Test" and outputs a Base64 encoded ciphertext.
 
.EXAMPLE
Invoke-AESEncryption -Mode Decrypt -Key "p@ssw0rd" -Text "LtxcRelxrDLrDB9rBD6JrfX/czKjZ2CUJkrg++kAMfs="
 
Description
-----------
Decrypts the Base64 encoded string "LtxcRelxrDLrDB9rBD6JrfX/czKjZ2CUJkrg++kAMfs=" and outputs plain text.
 
.EXAMPLE
Invoke-AESEncryption -Mode Encrypt -Key "p@ssw0rd" -Path file.bin
 
Description
-----------
Encrypts the file "file.bin" and outputs an encrypted file "file.bin.aes"
 
.EXAMPLE
Invoke-AESEncryption -Mode Decrypt -Key "p@ssw0rd" -Path file.bin.aes
```

#### Importing the PowerShell module

To use the script, transfer it to the target machine and import it as follows:

```
Import-Module .\Invoke-AESEncryption.ps1
```

#### File Encryption Example
This command encrypts **scan-results.txt** and generates **scan-results.txt.aes**.
```powershell
Invoke-AESEncryption -Mode Encrypt -Key "p4ssw0rd" -Path .\scan-results.txt
```

## File Encryption on Linux
OpenSSL is commonly used in Linux for encryption and security operations.
####  Encrypting a File
```bash
openssl enc -aes256 -iter 100000 -pbkdf2 -in /etc/passwd -out passwd.enc
```
In this command:
`-aes256` uses AES-256 encryption
`-iter 100000` increases password derivation iterations
`-pbkdf2` uses PBKDF2 to harden password security

#### Decrypting a File
```bash
openssl enc -d -aes256 -iter 100000 -pbkdf2 -in passwd.enc -out passwd
```


# Fun Facts
- PBKDF2 Prevents Rainbow Table Attacksby incorporating a salt. It ensures that precomputed hash attacks (rainbow tables) are ineffective.
# Sources
- https://blog.szurek.tv/post/pbkdf2-czyli-jak-spowolnic-atakujacych/
- https://www.pullrequest.com/blog/understanding-the-benefits-of-key-derivation-functions-a-deep-dive-into-pbkdf2/
- 