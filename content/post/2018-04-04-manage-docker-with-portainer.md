---
title: "Manage Docker with Portainer"
date: 2018-04-04T11:25:23+02:00
subtitle: "Portainer is a platform that really simplify management of docker environments"
author: Gabriele Teotino
tags: ["cloud", "linux", "debian", "docker", "portainer"]
categories: ["devops"]
draft: true
---

Portainer runs as a light (4Mb) container on Docker.

<!--more-->
## Installation
[Official docs](https://portainer.io/install.html)

Mounting the docker unix socket gives the container the full access to create and manipulate the host’s Docker daemon.

```shell
# create a volume
docker volume create portainer_data
# run portainer
docker run -d -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer
```

## Firewall
Now we need to open the port 9000. Let's check my external ip address and add a rule.

```shell
ufw allow from 94.81.51.199 port 9000
ufw status
```

## Run

Connect to the host with the browser, ti will ask for a new admin password.

Enjoy.

[Documentation](https://portainer.readthedocs.io/en/stable/index.html).
