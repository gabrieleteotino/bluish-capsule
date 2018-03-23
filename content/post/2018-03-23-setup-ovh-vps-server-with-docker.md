---
title: "Setup Ovh Vps server with Docker"
date: 2018-03-23T11:20:51+01:00
subtitle: "Basic setup of a vps server from OVH to be used as a development machine with Docker"
author: Gabriele Teotino
tags: ["cloud", "linux" "debian", "security", "docker"]
categories: ["devops"]
draft: true
---

I have a brand new VPS server from OVH with Debian Stretch. Let's configure it with some basic security and Docker. The server will be used as a test machine for my experiments. This machine is **not** for production.

<!--more-->

# Summary
- Update
- Secure root
- Secure ssh

## Update

Always check if new packages are available
```shell
ssh root@54.37.1.1 -p 22
apt update && apt upgrade -y
# reboot if necessary
```

## Securing root

Change the root password
```shell
ssh root@54.37.1.1 -p 22
passwd root
```

Create a new user
```shell
adduser gabriele
```

Disable root ssh access
```shell
nano /etc/ssh/sshd_config
# find the line: PermitRootLogin yes and change it to
PermitRootLogin no
# restart ssh
/etc/init.d/ssh restart
exit
```

Check that root cannot login
```shell
ssh root@54.37.1.1 -p 22
# after using the password you should see this message
Permission denied, please try again.
```

Login with another user
```shell
ssh gabriele@54.37.1.1 -p 22
su - root
```

## Secure ssh

Change ssh port
```shell
nano /etc/ssh/sshd_config
# change the line Port 22 to something else
Port 2222
# close the ssh connection and connect using the new port
ssh gabriele@54.37.1.1 -p 2222
```

Generate keys for ssh connection

[Arch linux reference](https://wiki.archlinux.org/index.php/SSH_keys)

On the local client machine
```shell
ssh-keygen -t ed25519 -C "$(whoami)@$(hostname)-$(date -I)"
# change the key name adding the server name
Enter file in which to save the key (/home/zap/.ssh/id_ed25519): /home/zap/.ssh/id_ed25519_servername
# put a nice passphrase
# copy the public key to the VPS
ssh-copy-id -i ~/.ssh/id_ed25519_servername -p 2222 gabriele@54.37.1.1
```

On the server check that the key was copied
```shell
cat ~/.ssh/authorized_keys
```

Try a connection using the key
```shell
ssh gabriele@54.37.1.1 -p 2222 -i ~/.ssh/id_ed25519_servername
```

Now that the key works disable password login
```shell
su -c nano /etc/ssh/sshd_config
# Change
PasswordAuthentication no
```

Managing multiple keys
It is possible —although not considered best practice— to use the same SSH key pair for multiple hosts.

On the other hand, it is rather easy to maintain distinct keys for multiple hosts by using the IdentityFile directive in your openSSH config file:

~/.ssh/config
Host SERVER1
   IdentitiesOnly yes
   IdentityFile ~/.ssh/id_rsa_SERVER1

Host SERVER2
   IdentitiesOnly yes
   IdentityFile ~/.ssh/id_ed25519_SERVER2
