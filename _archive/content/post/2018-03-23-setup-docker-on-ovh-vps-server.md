---
title: "Setup Docker on a OVH Vps server"
date: 2018-03-23T11:20:51+01:00
subtitle: "Basic setup of a vps server from OVH to be used as a development machine with Docker"
author: Gabriele Teotino
tags: ["cloud", "linux", "debian", "security", "docker"]
categories: ["devops"]
draft: false
---

I have a brand new VPS server from OVH with Debian Stretch. Let's configure it with some basic security and Docker. The server will be used as a test machine for my experiments. This machine is **not** for production.

<!--more-->

## Update

Check if new packages are available
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

### Change ssh port
```shell
nano /etc/ssh/sshd_config
# change the line Port 22 to something else
Port 2222
# close the ssh connection and connect using the new port
ssh gabriele@54.37.1.1 -p 2222
```

### Generate keys for ssh connection
([Arch linux reference](https://wiki.archlinux.org/index.php/SSH_keys))

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

### Disable password login
```shell
su -c nano /etc/ssh/sshd_config
# Change
PasswordAuthentication no
```

## Add the key to ssh-agent

Optional

On a secure machine it is possible to add the key to the agent

```shell
ssh-add ~/.ssh/id_ed25519_servername
  Enter passphrase for /home/zap/.ssh/id_ed25519_servername:
  Identity added: /home/zap/.ssh/id_ed25519_servername (comment)
```

Now to connect no password or passphrase is required
```shell
ssh gabriele@54.37.1.1 -p 2222
```

# Setup docker
[Offical instruction](https://docs.docker.com/install/linux/docker-ce/debian/)

Login as root

Add backport repository

```shell
vim /etc/apt/sources.list
# uncomment or add the following lines
# deb http://deb.debian.org/debian stretch-backports main contrib non-free
# deb-src http://deb.debian.org/debian stretch-backports main contrib non-free
wq
apt update
```

Add support for https repository

```shell
apt-get install \
  apt-transport-https \
  ca-certificates \
  curl \
  gnupg2 \
  software-properties-common
```

Add docker GPG key
```shell
curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add -
```

Verify that the key fingerprint is `9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88`

```shell
apt-key fingerprint 0EBFCD88

pub   rsa4096 2017-02-22 [SCEA]
      9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid           [ unknown] Docker Release (CE deb) <docker@docker.com>
sub   rsa4096 2017-02-22 [S]
```

Add the stable docker repository
```shell
add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/debian \
   $(lsb_release -cs) \
   stable"
```

Update and install docker-ce
```shell
apt update
apt install docker-ce
```

Test that docker is working
```shell
docker run hello-world
```

Enable at startup
```shell
# check if it is not enabled
systemctl is-enabled docker.service
# eventually enable it
systemctl enable docker.service
```
