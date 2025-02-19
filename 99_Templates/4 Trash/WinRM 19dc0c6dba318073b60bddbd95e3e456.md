# WinRM

The Windows Remote Management (`WinRM`) is a simple Windows integrated remote management protocol based on the command line. WinRM uses the Simple Object Access Protocol (`SOAP`) to establish connections to remote hosts and their applications. Therefore, WinRM must be explicitly enabled and configured starting with Windows 10. WinRM relies on `TCP` ports `5985` and `5986` for communication, with the last port `5986 using HTTPS`, as ports 80 and 443 were previously used for this task. However, since port 80 was mainly blocked for security reasons, the newer ports 5985 
and 5986 are used today.

Another component that fits WinRM for administration is Windows Remote Shell (`WinRS`), which lets us execute arbitrary commands on the remote system. The program is even included on Windows 7 by default. Thus, with WinRM, it is possible to execute a remote command on another server. Services like remote sessions using PowerShell and event log merging require WinRM. It is enabled by default starting with the `Windows Server 2012` version, but it must first be configured for older server versions and clients, and the necessary firewall exceptions created.

## Footprinting the Service

### **Nmap WinRM**

```bash
nmap -sV -sC 10.129.201.248 -p5985,5986 --disable-arp-ping -n
```

### Evil-WinRM

If we want to find out whether one or more remote servers can be reached via WinRM, we can easily do this with the help of PowerShell. The [Test-WsMan](https://docs.microsoft.com/en-us/powershell/module/microsoft.wsman.management/test-wsman?view=powershell-7.2) cmdlet is responsible for this, and the host's name in question is passed to it. In Linux-based environments, we can use the tool called [evil-winrm](https://github.com/Hackplayers/evil-winrm), another penetration testing tool designed to interact with WinRM.

```bash
evil-winrm -i 10.129.201.248 -u Cry0l1t3 -p P455w0rD!
```

# WMI

Windows Management Instrumentation (`WMI`) is Microsoft's implementation and also an extension of the Common Information Model (`CIM`), core functionality of the standardized Web-Based  Enterprise Management (`WBEM`) for the Windows platform. WMI allows read and write access to almost all settings on Windows systems. Understandably, this makes it the most critical interface in the Windows environment for the administration and remote maintenance of Windows computers, regardless of whether they are PCs or servers. WMI is typically accessed via PowerShell, VBScript, or 
the Windows Management Instrumentation Console (`WMIC`). WMI is not a single program but consists of several programs and various databases, also known as repositories.

## **Footprinting the Service**

The initialization of the WMI communication always takes place on `TCP` port `135`, and after the successful establishment of the connection, the communication is moved to a random port.

For example, the program [wmiexec.py](https://github.com/SecureAuthCorp/impacket/blob/master/examples/wmiexec.py) from the Impacket toolkit can be used for this.

### **WMIexec.py**

```bash
/usr/share/doc/python3-impacket/examples/wmiexec.py Cry0l1t3:"P455w0rD!"@10.129.201.248 "hostname"
```