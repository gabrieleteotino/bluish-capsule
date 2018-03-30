---
title: "Firewall on OVH Vps server"
date: 2018-03-30T10:55:51+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["cloud", "linux", "debian", "security", "ufw"]
categories: ["devops"]
draft: true
---

Installing and configuring the (Uncomplicated Firewall)[https://wiki.debian.org/Uncomplicated%20Firewall%20%28ufw%29]

<!--more-->

All the commands are executed by the root user.

Install
```shell
apt install ufw
```

Before starting the firewall we *have* to allow ssh traffic or we will closed out.

```shell
# if ssh is still on port 22
ufw allow ssh
# if it is on a custom port
ufw allow 2222/tcp
```

Add a few port for http and https
```shell
ufw allow 80/tcp
ufw allow 443/tcp
```

Check the rules and start the firewall
```shell
ufw show added
ufw enable
# check the status
ufw status
# with a bit more info
ufw status verbose
```

Done. It was uncomplicated.

A few other commands that can be useful

```shell
# Port ranges
ufw allow 1000:2000/tcp
ufw allow 1000:2000/udp
# IP address
ufw allow from 15.15.15.51
# Subnet
ufw allow from 15.15.15.0/24
# Deleting Rules
ufw delete allow ssh
# Deny an ip
ufw deny from 15.15.15.51
# Deny a subnet
ufw deny from 15.15.15.0/24
```
