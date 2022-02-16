---
title: "Manage Docker with Portainer"
date: 2018-04-04T11:25:23+02:00
subtitle: "Portainer is a platform that really simplify management of docker environments"
author: Gabriele Teotino
tags: ["cloud", "linux", "debian", "docker", "portainer"]
categories: ["devops"]
draft: false
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

Test portainer on port 9000.
Eventually open the port on the [Firewall](firewall)

## SSL

Generate certificates

```shell
# create a folder to put the keys
mkdir portainer
cd portainer
# make the key
openssl genrsa -out portainer.key 2048
openssl ecparam -genkey -name secp384r1 -out portainer.key
openssl req -new -x509 -sha256 -key portainer.key -out portainer.crt -days 3650
```

Rerun the container. It is possible to use the standard 443 port so the browser automatically uses https. I choose to leave the 443 free for other services.

```shell
docker run -d --name portainer -p 9000:9000 -v ~/portainer:/certs -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer --ssl --sslcert /certs/portainer.crt --sslkey /certs/portainer.key
```

## Firewall

Docker automatically opens al the ports mapped for the machine. No ufw rule is necessary.

## Run

Connect to the host with the browser. Use https if certificates are installed.

On the first run it will ask for a new admin password.

## Start/Stop

```shell
docker start portainer
docker stop portainer
```


Enjoy.

[Documentation](https://portainer.readthedocs.io/en/stable/index.html).
