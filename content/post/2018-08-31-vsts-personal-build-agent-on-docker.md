---
title: "VSTS personal build agent on docekr"
date: 2018-08-31T12:42:21.884+02:00
subtitle: "Using a personal build agent running on docker to speed up compilation times"
author: Gabriele Teotino
tags: ["cloud", "ovh", "vsts", "visualstudio", "docker", "ci", "continuous integration"]
categories: ["dev"]
draft: false
---

Visual Studio Team Services has a very good build agent: free and with high availability. The only problem is that it is a bit slow because every time you run a build it will start from scratch and download everything. A custom build agent will cache stuff and speed up build times a lot.

<!--more-->

For example I am experimenting on a build job that has to build an Angular + .net core API and create a couple docker images.

The steps I use for one of my jobs:

- Initialize agent + job: 20s
- Get sources: 5s
- Nuget package restore: 38s
- Build: 11s
- Publish: 4s
- Build docker image:28s
- Push docker image: 12s

Imagine that you are experimenting with a new job step and that every time you run a test you a have to wait a few minutes.

## Prepare permissions

Open your VSTS profile and go to your security details.

![Security settings](Screenshot_1_security.png)

Add a personal access token.

Put a description and choose a duration.

For the scope select **Agent Pools (read, manage)** and clear all the rest.

![Personal access token](Screenshot_2_personal_access_token.png)

Save the token in a safe place.

## Prepare Docker container

The official image is on docker hub [microsoft/vsts-agent](https://hub.docker.com/r/microsoft/vsts-agent/)

The standard image with all the tools is 4Gb compressed (check for disk space this is a hit on a VPS space)

Create a folder and add a new script

```shell
mkdir ~/vsts_agent
cd ~/vsts_agent
nano run_agent.sh
chmod +x run_agent.sh
```

Simple configuration for **run_agent.sh**

```shell
docker run \
  -e VSTS_ACCOUNT=<your organization name> \
  -e VSTS_TOKEN=<pat> \
  -it microsoft/vsts-agent:ubuntu-16.04
```

Advanced configuration for **run_agent.sh**

```shell
docker run \
  -e VSTS_ACCOUNT=<your organization name> \
  -e VSTS_TOKEN=<pat> \
  -e VSTS_AGENT='$(hostname)-agent' \
  -e VSTS_POOL=mypool \
  -e VSTS_WORK='/var/vsts/$VSTS_AGENT' \
  -v /var/vsts:/var/vsts \
  -it microsoft/vsts-agent:ubuntu-16.04
```

The image **ubuntu-16.04** has minimal capabilities but uses only 0.5Gb of uncompressed disk space.

Run the container

```shell
sh run_agent.sh
```

## Check the agent

Go in the Agent Pools section of VSTS to view the status and the capabilities offered by each agent.

![Agent pools](Screenshot_3_agent_pools.png)

## Use the agent

Edit a Build Job and change the *Agent Pool* to *Default* to use the agent.

Note that the basic agent **ubuntu-16.04** is not able to do much work because no extended capabilities are configured.
