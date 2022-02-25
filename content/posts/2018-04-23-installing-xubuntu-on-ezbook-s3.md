---
title: "Installing Xubuntu on Ezbook S3"
date: 2018-04-23T11:24:30+02:00
subtitle: "After a few experiments I decided to make full disk xubuntu installation."
author: Gabriele Teotino
tags: ["xubuntu", "ubuntu", "ezbook", "jumper", "banggood"]
categories: ["linux"]
draft: false
---

I bought a cheap laptop from china: [Ezbook 3S](https://www.banggood.com/Jumper-EZBOOK-3S-14_1-Inch-Laptop-Windows-10-Intel-Apollo-Lake-N3450-6GB-RAM-256GB-SSD-Storage-1080P-p-1184739.html?p=8V05105282880201607F).

After a few tests with live linux and a few installation on external devices I decided to wipe windows and use Xubuntu.

<!--more-->

I started with a [respin iso](2018-04-19-respin-xubuntu-for-ezbook-3s).

I made a full disk installation with full disk encryption and LVM.

Reboot

Change the DPI to 110 in Settings -> Appearance -> Fonts.
Change the default xfce theme in Settings -> Window Manager -> Style to Numix

Install the updates and reboot.

There is also a kernel upgrade so it will take a bit
```shell
sudo apt update && sudo apt get upgrade
```

Reboot

Download [lwfinger wifi driver](https://github.com/lwfinger/rtl8723bu)
```shell
sudo apt install git build-essential
mkdir drivers
cd drivers
git clone https://github.com/lwfinger/rtl8723bu.git
cd rtl8723bu
# Remove concurrent mode
nano Makefile
# Find and comment this line: EXTRA_CFLAGS += -DCONFIG_CONCURRENT_MODE
CTRL+x
make
sudo make install
sudo modprobe -v 8723bu
# blacklist the old driver
nano /etc/modprobe.d/blacklist.conf
# Add the following two lines at the end of the file
# Driver replaced by 8723bu by lwfinger
blacklist rtl8xxxu
CTRL+x
# Reload the new driver
sudo rmmod 8723bu
sudo modprobe -v 8723bu
# Check that the correct driver is in use
usb-devices
```

Reboot
