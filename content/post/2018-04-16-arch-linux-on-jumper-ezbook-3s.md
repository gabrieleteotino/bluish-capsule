---
title: "Arch Linux on Jumper Ezbook 3s"
date: 2018-04-16T10:58:23+02:00
subtitle: "Installing archlinux on a sdcard to test linux stability on this cheap notebok"
author: Gabriele Teotino
tags: []
categories: ["linux"]
draft: true
---

[Ezbook 3S](https://www.banggood.com/Jumper-EZBOOK-3S-14_1-Inch-Laptop-Windows-10-Intel-Apollo-Lake-N3450-6GB-RAM-256GB-SSD-Storage-1080P-p-1184739.html?p=8V05105282880201607F)


Connect to wifi hotspot
Connetion with ```wifi-menu``` doesn't work.
Select a network, insert a password and create a profile
A profile file will be created in /etc/netctl/profilename
netctl start profilename

ip link set wlp0s21f0u7i2 up
Find an access point
iw dev wlp0s21f0u7i2 scan | less
Test configuration generation for wpa
wpa_passphrase MYSSID passphrase
Connect
```shell
# some bug with the driver... it is necessary to unload and reload after every connection
rmmod rtl8xxxu
modprobe rtl8xxxu
wpa_supplicant -B -i wlp0s21f0u7i2 -c <(wpa_passphrase MYSSID passphrase)
```

Obtain a new address
dhcpcd wlp0s21f0u7i2

## partition
gdisk ...

## mount and load
lsblk -f

mkswap /dev/mmcblk2p3
swapon /dev/mmcblk2p3

mount /dev/mmcblk2p2 /mnt
mount /dev/mmcblk2p1 /mnt/boot

## mirror list
use Alt+arrow to switch to a new console
elinks https://www.archlinux.org/mirrorlist/
check the country and select generate list
Alt-f -> Save as -> ./mirrorlist
Make a backup of the original file
mv /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.old
Move the new mirrror list
mv ./mirrorlist /etc/pacman.d/mirrorlist
Edit the file and uncomment the preferred mirror
nano /etc/pacman.d/mirrorlist

## setup base packages
This is a bit long, about 20 minutes
pacstrap /mnt base

# Configure the system
Generate a new fstab
genfstab -U /mnt >> /mnt/etc/fstab

Chroot in the new environment
arch-chroot /mnt

Change timezione
ln -sf /usr/share/zoneinfo/Europe/Rome /etc/localtime
hwclock --systohc

Uncomment en_US.UTF-8 UTF-8
nano /etc/locale.gen
locale-gen

Create a new configuration file and set the LANG variable
nano /etc/locale.conf
LANG=en_US.UTF-8

The keyboard layout is ok with the default.

Create a hostname file
echo sunzi > /etc/hostname

Add the hostname "sunzi" to
nano /etc/hosts
127.0.0.1	localhost
::1		localhost
127.0.1.1	sunzi.localdomain	sunzi

## Network configuration
Install the packages for wireless configuration
pacman -S iw
pacman -S wpa_supplicant

## Final steps
Set the root password:
passwd

## boot loader
We need to install GRUB on the SD card instead of the SSD. rEFInd will locate the GRUB efi files and add them to the boot menu automatically.
