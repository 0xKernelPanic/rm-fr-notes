# SSH

[Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) (`SSH`) enables two computers to establish an encrypted and direct connection within a possibly insecure network on the standard port `TCP 22`. 

There are two competing protocols: `SSH-1` and `SSH-2`.

`SSH-2`, also known as SSH version 2, is a more advanced protocol than SSH version 1 in encryption, speed, stability, and securit y. For example, `SSH-1` is vulnerable to `MITM` attacks, whereas SSH-2 is not. OpenSSH has six different authentication methods:

1. Password authentication
2. Public-key authentication
3. Host-based authentication
4. Keyboard authentication
5. Challenge-response authentication
6. GSSAPI authentication

[https://www.golinuxcloud.com/openssh-authentication-methods-sshd-config/](https://www.golinuxcloud.com/openssh-authentication-methods-sshd-config/)

## **Dangerous Settings**

| **Setting** | **Description** |
| --- | --- |
| `PasswordAuthentication yes` | Allows password-based authentication. |
| `PermitEmptyPasswords yes` | Allows the use of empty passwords. |
| `PermitRootLogin yes` | Allows to log in as the root user. |
| `Protocol 1` | Uses an outdated version of encryption. |
| `X11Forwarding yes` | Allows X11 forwarding for GUI applications. |
| `AllowTcpForwarding yes` | Allows forwarding of TCP ports. |
| `PermitTunnel` | Allows tunneling. |
| `DebianBanner yes` | Displays a specific banner when logging in. |

https://www.ssh-audit.com/hardening_guides.html

## **Footprinting the Service**

### ssh-audit

One of the tools we can use to fingerprint the SSH server is [ssh-audit](https://github.com/jtesta/ssh-audit).
 It checks the client-side and server-side configuration and shows some 
general information and which encryption algorithms are still used by 
the client and server.

```bash
git clone https://github.com/jtesta/ssh-audit.git && cd ssh-audit
./ssh-audit.py 10.129.14.132
```

**Change Authentication Method**

```bash
ssh -v cry0l1t3@10.129.14.132
ssh -v cry0l1t3@10.129.14.132 -o PreferredAuthentications=password
```

### SSH Banner

We may encounter various banners for the SSH server during our penetration tests. By default, the banners start with the version of the protocol that can be applied and then the version of the server itself. For example, with `SSH-1.99-OpenSSH_3.9p1`, we know that we can use both protocol versions SSH-1 and SSH-2, and we are dealing with OpenSSH server version 3.9p1. On the other hand, for a banner with `SSH-2.0-OpenSSH_8.2p1`, we are dealing with an OpenSSH version 8.2p1 which only accepts the SSH-2 protocol version.