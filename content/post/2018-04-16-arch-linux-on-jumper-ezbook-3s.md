---
title: "Arch Linux on Jumper Ezbook 3s"
date: 2018-04-16T10:58:23+02:00
subtitle: "Installing archlinux on a sdcard to test linux stability on this cheap notebok"
author: Gabriele Teotino
tags: ["arch", "archlinux", "ezbook", "jumper", "rEFInd", "banggood"]
categories: ["linux"]
draft: false
---

I bought a cheap laptop from china: [Ezbook 3S](https://www.banggood.com/Jumper-EZBOOK-3S-14_1-Inch-Laptop-Windows-10-Intel-Apollo-Lake-N3450-6GB-RAM-256GB-SSD-Storage-1080P-p-1184739.html?p=8V05105282880201607F).

It features a nice 14" display, a slow but usable Intel Apollo Lake N3450, 6Gb of ram and a 256Gb SSD.

I want to try Arch Linux on an external usb drive, just to make a few test before committing to the full wipe of Windows 10 Home. Reports from other users says that wifi is working with an alternate driver but no bluetooth. Other periferals should work just fine.

I also tried to install on a SD card but rEFInd is not able to detect the card controller.

<!--more-->

# Preparation

## Windows reset
I made a full reset from the control panel.

To prevent filesystem corruption disable fast shutdown. Open and administrative cmd.
```
powercfg /h off
```

## rEFInd

[Download rEFInd](http://www.rodsbooks.com/refind/getting.html)
Extract the zip

Follow the manual [installation guide](http://www.rodsbooks.com/refind/installing.html#windows)

Open and administrative cmd.
```
mountvol S: /S
```

Change into the main rEFInd package directory

```
xcopy /E refind S:\EFI\refind\
S:
cd EFI\refind
rename refind.conf-sample refind.conf
bcdedit /set "{bootmgr}" path \EFI\refind\refind_x64.efi
bcdedit /set "{bootmgr}" description "rEFInd"
```

# Arch

## Wifi connection
The network driver is bugged. To make it work it is necessary to unload and reload after every connection. Sometimes it's necessary to unload and load a few times.
```shell
rmmod rtl8xxxu
modprobe rtl8xxxu
```

### Connection with `wifi-menu`
```shell
wifi-menu
# Select a network, insert a password and create a profile
# A profile file will be created in /etc/netctl/profilename
netctl start profilename
```

### Connection with `wpa_supplicant`

Find an access point
```shell
ip link set wlp0s21f0u7i2 up
iw dev wlp0s21f0u7i2 scan | less
```

Test configuration generation for wpa
```shell
wpa_passphrase MYSSID passphrase
```

Connect
```shell
wpa_supplicant -B -i wlp0s21f0u7i2 -c <(wpa_passphrase MYSSID passphrase)
```

### Ip and test
Obtain a new address and test the connection
```shell
dhcpcd wlp0s21f0u7i2
ping www.archlinux.org
```

## Partitioning
Find the block device to use
```shell
lsblk
```

Create an EFI partition of 550Mb and ext partition
```shell
gdisk /dev/sdc
n 550M ef00
n all space 8300
w
# format
mkfs.vfat /dev/sdc1
mkfs.ext4 /dev/sdc2
# view the result
lsblk -f
```

mount
```shell
mount /dev/sdc2 /mnt
mount /dev/sdc1 /mnt/boot
```

## mirror list
Use Alt+arrow to switch to a new console
```shell
elinks https://www.archlinux.org/mirrorlist/
```
Check the country and select generate list

Alt-f -> Save as -> ./mirrorlist

Update the mirror list
```shell
# Make a backup of the original file
mv /etc/pacman.d/mirrorlist /etc/pacman.d/mirrorlist.old
# Move the new mirrror list
mv ./mirrorlist /etc/pacman.d/mirrorlist
# Edit the file and uncomment the preferred mirror
nano /etc/pacman.d/mirrorlist
```

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
```shell
pacman -S iw
pacman -S wpa_supplicant
# needed for wifi-menu
pacman -S dialog
```

# Final steps

## Set the root password
passwd

## Boot loader
We install GRUB on the Usb drive instead of the SSD. rEFInd will automatically add GRUB to the boot menu.

```shell
pacman -S grub
pacman -S efibootmgr
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=arch_grub
grub-mkconfig -o /boot/grub/grub.cfg
```

## Reboot
umount /dev/sdc1
umount /dev/sdc2
reboot

## Say thanks
Make a donation to rodsmith for the work done on rEFInd.
