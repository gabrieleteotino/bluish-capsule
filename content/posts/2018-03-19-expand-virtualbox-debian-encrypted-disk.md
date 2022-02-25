---
title: "Expand VirtualBox Debian encrypted disk"
date: 2018-03-19T09:40:14+01:00
subtitle: "Expand a luks encrypted root partition in a Debian Stretch Virtualbox machine."
author: Gabriele Teotino
tags: ["debian", "virtualbox", "encryption", "luks", "security"]
categories: ["linux"]
draft: false
---
I have a VirtualBox machine with full disk encryption. The space for the root partition is almost finished.

<!--more-->

## Expand VirtualBox vdi size
Remove all snapshots or clone the machine.
Make a backup copy.

### Expand from command line
The command to resize the disk is

```shell
VBoxManage modifymedium <absolute path to file> --resize <size in MB>
```

If VBoxManage is not in PATH add the full path

```shell
"C:\Program Files\Oracle\VirtualBox\VBoxManage" modifymedium disk "D:\VirtualBox VMs\DebianBP Zap\DebianBP Zap.vdi" --resize 40000
```

### Expand from VirtualBox GUI
- Open the application
- File -> Virtual Media Manager
- Click on Properties
- Select the disk
- Select the new size
- Click Apply

![VirtualBox resize disk](virtualbox-resize-disk.png)

## Live CD
- Download [System rescue cd](http://www.system-rescue-cd.org/Download/)
- Attach the image to the virtual machine
- Boot
- Choose "start the graphical environment"

![graphical environment](systemrescuecd-boot-options.png)

- Keymap for italian keyboard is 21

## Resize
Original steps from [stackexchange](https://unix.stackexchange.com/a/322631)

Actual partitioning of sda is

- 1 256MB primary boot
- 2 32GB extended
  - 5 32GB logical

Modified steps for my partition layout

1. open the encrypted volume

    ```shell
    cryptsetup luksOpen /dev/sda5 crypt-volume
    ```
2. extend the partition

    ```shell
    parted /dev/sda
    resizepart 2 100%
    resizepart 5 100%
    quit
    ```
3. stop using the VG

    ```shell
    # display attributes of volume groups, the name zap-bp-vg is from the "VG Name"
    vgdisplay
    # deactivate the VG
    vgchange -a n zap-bp-vg
    ```
4. close the encrypted volume

    ```shell
    cryptsetup luksClose crypt-volume
    ```
5. open it again

    ```shell
    cryptsetup luksOpen /dev/sda5 crypt-volume
    ```
6. resize the LUKS volume to the available space

    ```shell
    cryptsetup resize crypt-volume
    ```
7. Activate the VG

    ```shell
    vgchange -a y zap-bp-vg
    ```
8. Resize the PV

    ```shell
    pvresize /dev/mapper/crypt-volume
    ```
9. Resize the LV for / to 100% of the free space

    ```shell
    # display attributes of a logical volume, the name /dev/zap-bp-vg/root is from the "LV Path"
    lvdisplay
    lvresize -l+100%FREE /dev/zap-bp-vg/root
    ```
10. check the fs

    ```shell
    # find the volume path
    ls /dev/mapper/
    e2fsck -f /dev/mapper/zap--bg--vg-root
    ```
11. resize the filesystem (automatically uses 100% free space)

    ```shell
    resize2fs /dev/mapper/zap--bg--vg-root
    ```

Reboot and remove the *System rescue cd* image.
