---
title: "VSTS minimal docker build agent"
date: 2018-09-03T11:36:11.054+02:00
subtitle: "The default visual studio team service build agent image is too big. By removing some unused tools let's see if we can limit the size."
author: Gabriele Teotino
tags: ["cloud", "ovh", "vsts", "visualstudio", "docker", "ci", "continuous integration"]
categories: ["dev"]
draft: true
---

The official standard image with all the tools is 4Gb compressed and it is really too big for my tiny VPS server. The image includes a lot of tools that I don't use so I will remove those tools a see ho much disk space I can save.

<!--more-->

## Repository

The repository with all the experiments and images is [vsts-minimal-build-agent](https://github.com/gabrieleteotino/vsts-minimal-build-agent)

## Ubuntu 18.04

In the [offical Microsoft docker hub](https://hub.docker.com/r/microsoft/vsts-agent/) the base image uses [Ubuntu 16.04](https://github.com/Microsoft/vsts-agent-docker/blob/master/ubuntu/16.04/Dockerfile).

```shell
docker image ls microsoft/vsts-agent:ubuntu-16.04
REPOSITORY             TAG                 IMAGE ID            CREATED             SIZE
microsoft/vsts-agent   ubuntu-16.04        929c015071d9        8 days ago          304MB
```

Let try to upgrade the base version to 18.04 (just because)

From the original configurations I removed some library: libcurl3, libicu55, libunwind8, the I moved other components and configurations from the standard image to the base image. A bigger and more complete base image will reduce the time needed for building the other optimized images.

Results

```shell
docker build -t gabrieleteotino/vsts-minimal-agent:ubuntu-18.04 ubuntu/18.04
docker image ls gabrieleteotino/vsts-minimal-agent
REPOSITORY                           TAG                 IMAGE ID            CREATED             SIZE
gabrieleteotino/vsts-minimal-agent   ubuntu-18.04        4f97492b2081        35 seconds ago      321MB
```

```shell
docker build -t gabrieleteotino/vsts-minimal-agent:ubuntu-18.04-core-node ubuntu/18.04/core-node
docker image ls gabrieleteotino/vsts-minimal-agent

```
