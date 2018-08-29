---
title: "Visual Studio Online to DockerHub"
date: 2018-08-27T15:49:56.486+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

## Preparation

Register to [Docker Hub](https://hub.docker.com/), the free plan offers unlimited public images and one private repository.

Register to [Visual Studio Online](https://visualstudio.microsoft.com/it/team-services/) the free plan is very powerful and allows for unlimited private repositories.


## Build

Remove the *Phase* **Publish Artifacts**
Change the build context to DatingApp.API
Check **Include Latest Tag**

For the second *Task*
Change the *Action* to **Push an image**

## Deploy

Connect via ssh to the OVH virtual machine.

Clone the repo

```shell
git clone https://github.com/gabrieleteotino/datingapp-docker.git
```

Prepare configuration

```shell
cp datingapp_example.env datingapp.env
nano datingapp.env
# enter a random string for jwt and cloudinary credentials
```

Compose

```shell
# build the image
docker-compose build
# run interactively
docker-compose up
# Or build and run in background
docker-compose up -d --build
```
