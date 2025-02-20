---
title: dnf
tags:
  - repository
  - package_manager
  - rhel
  - command
  - proxy
  - yum
  - dnf
created: 2025-02-20
---

# dnf

## Description
The ****`dnf`**** command is a package manager command in Red-based Linux distros for example fedora, Cent OS and RHEL. It stands for "Dandified YUM," where `YUM` is another package manager commonly used in these distributions.
## Commands

```bash
dnf search <package_name> # Search for package
dnf install <package_name> <another_package> # Install the packages
dnf remove <package_name> <another_package> # Remove the package
dnf upgrade # Checks the repositories for newer packages and updates them

dnf autoremove #removes dependencies that are no longer required
dnf repolist
dnf info <package_name> 
dnf list installed | grep "Apache" # Checks if apache is installed
dnf deplist <package_name> # View dependency tree before installation
```


Other common DNF commands include:
- **check-update*** - checks for updates, but does not download or install the packages.
- **downgrade** - reverts to the previous version of a package.    
- **reinstall** - reinstalls the currently installed package.
### Excluding Packages
DNF allows you to exclude a package from the transaction.
Using the command line:
```bash
dnf upgrade --exclude=<package_name>
```

Alternatively you can use configuration file in `/etc/dnf/dnf.conf` or `/etc/yum.repos.d/`
```conf
excludepkgs=<packagename>
```

### Repository configuration
Repositories are stored in `/etc/yum.repos.d/` with the **.repo suffix**. You can add them manually or via:
```bash
dnf config-manager --add-repo <repository_URL>
```

#### Example repository can look like this
```txt
[base]
name=base
baseurl=http://example.com/repo/rhel9.0/x86_64/BaseOS
gpgcheck=0
enabled=1
#gpgkey=http://example.com/repo/rhel9.0/RPM-GPG-KEY


[app]
name=app
baseurl=http://example.com/repo/rhel9.0/x86_64/AppStream
gpgcheck=0
enabled=1
#gpgkey=http://example.com/repo/rhel9.0/RPM-GPG-KEY
```

#### Repository with authentication
If the repository requires authentication. modify the `baseurl` as follows:
```txt
baseurl=http://username:password@example.com/repo/
```

Alternatively, you can securly store credentials in a custom repo file:
```txt
[base]
name=base
baseurl=http://example.com/repo/rhel9.0/x86_64/BaseOS
gpgcheck=0
enabled=1
username=username
password=password
[...]
```

Make sure to set appropriate permission:
```bash
sudo chmod 600 /etc/yum.repos.d/repo
```
#### Repository from a file
If the repo is provided as a local file, use the `file://`:
```txt
baseurl=file:///path/to/local/repo
```

Ensure the file is accessible:
```bash
sudo chmod -R 755 /path/to/local/repo/
```

#### Updates via proxy
You can make dnf updates via proxy through repo config
```txt
#Proxy settings
proxy=http://proxy.example.com:3128
proxy_username=username
proxy_password=password
```


### Handling Broken Transactions
If a package update fails or causes dependency issues:
```bash
dnf history # List latest transactions
dnf history undo last # Undo last action
dnf history list <package_name> # Check the latest operations on a package
dnf history rollback <transaction_id> # Undo all changes to a id state 

dnf clean all # cleans metadata
```

### Automatic Updates
The `dnf-automatic` package is a component that allows automatic download and installation of updates. It can automatically monitor and report, via e-mail, the availability of updates or send a log about downloaded packages and installed updates.
For more information, refer to the [Read the Docs: DNF-Automatic](https://dnf.readthedocs.org/en/latest/automatic.html) page.



## Sources
- https://docs.fedoraproject.org/en-US/quick-docs/dnf/
- https://www.geeksforgeeks.org/dnf-command-in-linux/
- https://dnf.readthedocs.io/en/latest/command_ref.html
- https://docs.oracle.com/en/operating-systems/oracle-linux/6/admin/ol_repo_config.html
- https://docs.chef.io/resources/yum_repository/
- https://docs.redhat.com/en/documentation/red_hat_enterprise_linux/9/html/managing_software_with_the_dnf_tool/assembly_handling-package-management-history_managing-software-with-the-dnf-tool#assembly_handling-package-management-history_managing-software-with-the-dnf-tool
