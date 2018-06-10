---
title: "Restore xubuntu initramfs"
date: 2018-06-10T19:20:57+02:00
subtitle: ""
author: Gabriele Teotino
tags: []
categories: []
draft: true
---

For some reason my boot stopped after unlocking my luks encrypted drive.

<!--more-->

Boot from a live xubuntu 17.10.

Unlock encrypted disk

```shell
sudo su
cryptsetup luksOpen /dev/sda3 sda3_crypt

vgscan
#  Reading volume groups from cache.
#  Found volume group "xubuntu-vg" using metadata type lvm2

vgchange -ay
#  2 logical volume(s) in volume group "xubuntu-vg" now active

lvscan
#  ACTIVE            '/dev/xubuntu-vg/root' [231.37 GiB] inherit
#  ACTIVE            '/dev/xubuntu-vg/swap_1' [5.84 GiB] inherit

ls /dev/mapper/
#control  xubuntu--vg-root  xubuntu--vg-swap_1  xubuntu-vg
```

Chroot

```shell
mount /dev/xubuntu-vg/root /mnt

mount --bind /proc /mnt/proc
mount --bind /dev /mnt/dev
mount --bind /sys /mnt/sys
# I actually forgot this step and it went fine
mount --bind /dev/pts /mnt/dev/pts

chroot /mnt

mount /dev/sda2 /boot
mount /dev/sda1 /boot/efi
```

Fix the boot process

```shell
update-initramfs -u -k all
update-grub2
grub-install /dev/sda
```

Exit
```
umount /boot/efi
umount /boot

#leave chroot
exit

umount -l /mnt
vgchange -an
cryptsetup luksClose xubuntu-vg
```
