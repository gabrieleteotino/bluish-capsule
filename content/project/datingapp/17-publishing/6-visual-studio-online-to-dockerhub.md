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
