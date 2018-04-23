---
title: "Respin Lubuntu for Ezbook 3s"
date: 2018-04-19T16:50:37+02:00
subtitle: "Isorepin is a script that can customize an ISO to be able to boot on apollo lake systems."
author: Gabriele Teotino
tags: ["arch", "archlinux", "ezbook", "jumper", "rEFInd", "banggood"]
categories: ["linux"]
draft: false
---

I bought a cheap laptop from china: [Ezbook 3S](https://www.banggood.com/Jumper-EZBOOK-3S-14_1-Inch-Laptop-Windows-10-Intel-Apollo-Lake-N3450-6GB-RAM-256GB-SSD-Storage-1080P-p-1184739.html?p=8V05105282880201607F).

To boot Lubuntu a bit of trickery has to be done on the iso image.

<!--more-->

Official [IsoRespin instructions](http://www.linuxium.com.au/how-tos/creatingpersonalizedubuntumintanddebianisosforintelminipcs).

Download [isorespin.sh](https://goo.gl/ZnUd6H)

```shell
cd ~/Downloads/
chmod +x isorespin.sh
# Official dependency list
#sudo apt -y install bc curl losetup ip isoinfo mkdosfs mksquashfs rsync
#sudo apt -y install unsquashfs unzip wget xargs xorriso

sudo apt -y install xorriso

# Respin the iso with apollo support
./isorespin.sh -i /home/zap/Downloads/xubuntu-17.10.1-desktop-amd64.iso --apollo
```

Wrote the image using Rufus in iso mode.
