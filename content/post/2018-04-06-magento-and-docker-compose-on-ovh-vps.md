---
title: "Magento and Docker Compose on OVH Vps"
date: 2018-04-06T11:40:03+02:00
subtitle: "Installing and configuring docker compose on Debian Stretch running on a VPS"
author: Gabriele Teotino
tags: ["cloud", "linux", "debian", "security", "docker"]
categories: ["devops"]
draft: false
---

Compose defines and runs multi-container Docker applications.

Using a single yaml file it is possible to run multiple docker containers for complex applications. For example one contaner for the web app, one for the database and one for redis, all with a single command.

<!--more-->

## Setup

Official [Installation guide](https://docs.docker.com/compose/install/)

Don't use the repository like in the [docker installation](2018-03-23-setup-docker-on-ovh-vps-server), it is not up to date.

Check the [release page](https://github.com/docker/compose/releases) to download the latest version.

The official installation instruction suggest to install in /usr/local/bin, I ignored that.
```shell
curl -L https://github.com/docker/compose/releases/download/1.21.0-rc1/docker-compose-Linux-x86_64 -o /usr/bin/docker-compose
chmod +x /usr/bin/docker-compose
```

## Magento2 with compose

[Original git repo](https://github.com/alexcheng1982/docker-magento2)

Create a folder for the project and download compose and environment files
```shell
mkdir magento2
cd magento2
curl https://raw.githubusercontent.com/alexcheng1982/docker-magento2/master/docker-compose.yml -o docker-compose.yml
curl https://raw.githubusercontent.com/alexcheng1982/docker-magento2/master/env -o env
```

The yaml file with modified port mapping
```yaml
version: '3.0'
services:
  web:
    image: alexcheng/magento2
    ports:
      - "8080:80"
    links:
      - db
    env_file:
      - env
  db:
    image: mysql:5.6.23
    volumes:
      - db-data:/var/lib/mysql/data
    env_file:
      - env
  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    ports:
      - "8580:80"
    links:
      - db
volumes:
  db-data:
```

Run it
```shell
# run in daemon mode
docker-compose up -d
```
