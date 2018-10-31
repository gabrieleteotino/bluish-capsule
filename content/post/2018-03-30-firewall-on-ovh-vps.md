---
title: "Firewall on OVH Vps server"
date: 2018-03-30T10:55:51+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["cloud", "linux", "debian", "security", "ufw"]
categories: ["devops"]
draft: false
---

Installing and configuring the [Uncomplicated Firewall](https://wiki.debian.org/Uncomplicated%20Firewall%20%28ufw%29)

<!--more-->

All the commands are executed by the root user.

## Installation
```shell
apt install ufw
```

## Configuration
Before starting the firewall we *have* to allow ssh traffic or we will close ourself out.

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

# Remove a rule

View a numbered list of rules and remove the rule using the index
```shell
ufw status numbered
  Status: active

     To                         Action      From
     --                         ------      ----
  [ 1] 2222/tcp                   ALLOW IN    Anywhere
  [ 2] 80                         ALLOW IN    Anywhere
  [ 3] 443                        ALLOW IN    Anywhere
  [ 4] Anywhere                   ALLOW IN    94.81.51.199 9000
  [ 5] 2222/tcp (v6)              ALLOW IN    Anywhere (v6)
  [ 6] 80 (v6)                    ALLOW IN    Anywhere (v6)
  [ 7] 443 (v6)                   ALLOW IN    Anywhere (v6)

ufw delete 4
```

## More
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
