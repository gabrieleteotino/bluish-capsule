---
title: "Nextcloud using docker on ovh vps"
date: 2019-02-05T13:01:25.491+01:00
subtitle: "Installing Nexctcloud personal cloud server using docker on a virtual private server hosted by OVH"
author: Gabriele Teotino
tags: ["nextcloud", "cloud", "docker", "debian", "linux", "ovh"]
categories: ["cloud"]
draft: true
---

[Nextcloud](https://nextcloud.com/) is a private cloud service platform. I plan to use it for personal and family usage, just to move some of my personal data away from third parties.

<!-- more -->

There is an official build for docker [source](https://github.com/nextcloud/docker) [dockerhub](https://hub.docker.com/_/nextcloud/).

There is a good starting example in github (i tried the postgres version but it has an open bug and the workaround is not worth my time).

```shell
mkdir nextcloud
cd nextcloud
mkdir git
cd git
git clone https://github.com/nextcloud/docker.git
cp docker/.examples/docker-compose/with-nginx-proxy/mariadb/apache/* ../ -r
cd ..
```

Now in the folder *~/nextcloud* we have the git clone and the directory. It is possible to remove the git folder.

```shell
rm -r git
```

Edit **db.env** and put a new strong password for the database user

Edit **docker-compose.yml** and

- add a strong password for *MYSQL_ROOT_PASSWORD=*
- insert your nextcloud domain behind *VIRTUAL_HOST=* and *LETSENCRYPT_HOST=* (yes the same cloud.domain.tld in both fields)
- enter a valid email behind *LETSENCRYPT_EMAIL=*

Build the images

```shell
docker-compose build --pull
```

Run

```shell
docker-compose up -d
```

Open the browser to the address specified in *VIRTUAL_HOST=* and create an admin user.

## OTHER stuff

From the official [examples](https://github.com/nextcloud/docker/tree/master/.examples)

Enhance security by disabling preview generation
https://docs.nextcloud.com/server/12/admin_manual/configuration_server/harden_server.html?highlight=enabledpreviewproviders#disable-preview-image-generation

Eventually install libreoffice (about +500Mb)
