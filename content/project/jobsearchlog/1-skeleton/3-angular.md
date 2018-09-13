---
title: "Angular"
date: 2018-09-13T16:03:33.372+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular"]
categories: ["dev"]
draft: true
---

<!--more-->

## Updatating Angular

Update npm and ng to the latest version (at the time of writing 6.4.0 and @angular/cli@6.2.1)

```shell
sudo npm update npm -g
sudo npm install npm -g
sudo npm install -g @angular/cli@latest
```

## Angular setup
Create a new Angular application inside the cloned git folder

```shell
cd ~/dev/JobSearchLog
ng new JobSearchLog --directory ./
```

In the *package.json* file we can see that the default scripts for npm are already configured to launch ng with the appropriate parameters.

Run the Angular application and check that everything is working

```shell
ng serve
```

Navigate with the browser to http://localhost:4200/

Make a git commit with new files.
