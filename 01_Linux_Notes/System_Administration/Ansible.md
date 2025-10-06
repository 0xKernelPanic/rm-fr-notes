---
title: Ansible
tags:
  - automation
created: 2025-10-06
---

# Ansible

## Description
Ansible is an open-source IT automation tool used for configuration management, application deployment, and task automation. It allows you to define infrastructure as code and manage multiple systems simultaneously without requiring agents on the target machines.
## Use Cases
- Automating server configuration and software installation across multiple environments.
- Deploying applications consistently across development, staging, and production.
### Inventory (Hosts) File

Defines which systems Ansible manages. Example:
```ini
[linux]
192.168.47.130
192.168.47.129

[linux:vars]
ansible_user=your_username
ansible_password=your_password
```
> [!Warning]
Use SSH keys instead of passwords for better security.


### Configuration File (ansible.cfg)
Basic example:
```ini
[defaults]
inventory = ./hosts
remote_user = your_username
host_key_checking = False
```
### Playbooks
Ansible **playbooks** are YAML files that define tasks for hosts. 
Example:

```yml
- name: Install htop on Oracle Linux 10
  hosts: linux
  become: yes
  tasks:
    - name: Enable EPEL repository
      dnf:
        name: oracle-epel-release-el10
        state: present

    - name: Install htop
      dnf:
        name: htop
        state: latest # or absent
```
## Commands
Test connectivity

```bash
ansible linux -m ping
```

Run a command on remote hosts
```bash
ansible linux -a "cat /etc/os-release"

ansible linux -a "reboot"
```

Run playbook
```bash
ansible-playbook iluvnano.yml 
```
## Fun Facts
- Ansible is **agentless**, meaning it doesn’t require additional software installed on target machines.
- It uses **SSH for Linux** and **WinRM for Windows** to communicate with managed nodes.

## Sources
- https://www.youtube.com/watch?v=5hycyr-8EKs
