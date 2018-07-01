---
title: "Installations"
date: 2018-05-30T21:55:24+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular", "postman", "sqlite", "node", "nvm"]
categories: ["dev"]
draft: false
---

Installation of all the basic tools.

<!--more-->

# C\#

## .Netcore

```shell
sudo apt install dotnet-sdk-2.1.300-rc1-008673
```

## Visual Studio Code

[download](https://code.visualstudio.com/docs/setup/linux) then install the *deb* package.

Installing the .deb package will automatically install the apt repository and signing key to enable auto-updating using the regular system mechanism

```shell
sudo dpkg -i <file>.deb
sudo apt-get install -f # Install dependencies
```

# Angular

## Node

I installed node with nvm. [nvm article]({{< relref "post/2018-05-28-install-nodejs-using-nvm.md" >}})

```shell
nvm install --lts
```

The LTS version at the time was 8.11.2

## Angular

[Angular cli](https://cli.angular.io/). I used the exact version used in the udemy course. After chapter 5 I updated to angular 6.

```shell
npm install -g @angular/cli@1.7.4
```

# Tools

## Postman

[Postman article]({{< relref "note/2018-05-30-postman-api-ide.md" >}})

## DB Browser for SQLite

[DB Browser for SQLite article]({{< relref "note/2018-05-31-db-browser-for-sqlite.md" >}})
