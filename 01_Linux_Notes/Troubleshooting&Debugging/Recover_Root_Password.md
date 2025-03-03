---
title: Recover root password
tags:
  - rhel
  - troubleshooting
created: 2025-02-20
---

# Recover Root Password

## Description
Recovering the root password on a RHEL/CentOS 7+ system requires physical access to the machine and modification of the GRUB bootloader.

## Steps
1. **Reboot the System**: As soon as the GRUB bootloader appears, press the up/down arrow keys to stop the countdown.
2. **Edit Boot Parameters**:
	- Select the kernel to boot into and press e.
	- Find the line starting with `linux` and press **CTRL + E** to move the cursor to the end of the line and append `rd.break`.
3. **Boot into Rescue Mode**: Press `Ctrl-x` to boot with the modified parameters.
4. **Remount the Root Filesystem**:
```bash
mount -o remount,rw /sysroot
```
5. **Enter Chroot Environment**:
```bash
chroot /sysroot
```
6. **Change the Root Password**:
```bash
passwd
```
7. **Relabel SELinux** (to ensure the password change is recognized):
```bash
touch /.autorelabel
```
8. **Exit and Reboot**:
```bash
exit
reboot
```

## Sources
- https://www.redhat.com/en/blog/recover-root-passwd

