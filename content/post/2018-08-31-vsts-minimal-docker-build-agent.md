---
title: "VSTS minimal docker build agent"
date:
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

## Base image

In the [offical Microsoft docker hub](https://hub.docker.com/r/microsoft/vsts-agent/) the base image uses [Ubuntu 16.04](https://github.com/Microsoft/vsts-agent-docker/blob/master/ubuntu/16.04/Dockerfile).

```shell
docker image ls microsoft/vsts-agent:ubuntu-16.04
REPOSITORY             TAG                 IMAGE ID            CREATED             SIZE
microsoft/vsts-agent   ubuntu-16.04        929c015071d9        8 days ago          304MB
```

Let's try to create a basic agent using [alpine](https://hub.docker.com/_/alpine/)
