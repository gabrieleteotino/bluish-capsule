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

From the original configurations I moved a few components and configurations from the standard image to the base image. A bigger and more complete base image will reduce the time needed for building the other optimized images.

Results

```shell
docker build -t gabrieleteotino/vsts-minimal-agent:ubuntu-18.04 ubuntu/18.04
docker build -t gabrieleteotino/vsts-minimal-agent:ubuntu-18.04-core-node ubuntu/18.04/core-node
docker image ls gabrieleteotino/vsts-minimal-agent
REPOSITORY                           TAG                      IMAGE ID            CREATED             SIZE
gabrieleteotino/vsts-minimal-agent   ubuntu-18.04-core-node   cc9eeb17ee72        25 minutes ago      2.2GB
gabrieleteotino/vsts-minimal-agent   ubuntu-18.04             6264c2f8ddec        37 minutes ago      472MB
```

## Test the basic agent

Create a script **vsts_minimal_agent_core_node.sh** with the following content

```shell
docker run \
  -e VSTS_ACCOUNT=<your organization name> \
  -e VSTS_TOKEN=<pat> \
  -e VSTS_AGENT='donbosco-agent-01' \
  -e VSTS_POOL=Donbosco \
  -it gabrieleteotino/vsts-minimal-agent:ubuntu-18.04-core-node
  --name vsts_agent_01
```

Note that this test can be done using the **Default** agent pool.

From VSTS site go in **Agent pools** and create a new *agent pool* in my case named **Donbosco** (it has to corrispond to the **VSTS_POOL** parameter)

Now run the agent

```shell
bash vsts_minimal_agent_core_node.sh
```

On VSTS create a new build script and change the Agent pool for the pipeline to **Donbosco** insert some step the *Save and queue*.

This build script will download a git repo, do a *dotnet restore* then a *dotnet ef migrations script*

In the docker TTY will appear

```shell
2018-09-05 10:09:13Z: Running job: Job
2018-09-05 10:09:59Z: Job Job completed with result: Succeeded
```

If we queue the job again
```shell
2018-09-05 10:10:46Z: Running job: Job
2018-09-05 10:11:08Z: Job Job completed with result: Succeeded
```

46 seconds for the first run and 22 second for the second run. The 24 second difference are mostly from the package restore step.

The same build running time on Microsoft Hosted Agent takes 1m 32s.

The difference became really noticeable with bigger jobs.
