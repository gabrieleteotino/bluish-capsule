---
title: "Git clone a specific commit"
date: 2018-07-27T10:59:45.884+02:00
subtitle: "Downloading a specific commit from github"
author: Gabriele Teotino
tags: ["git", "github"]
categories: ["dev"]
draft: false
---

I need to download a specific version of a project from *GitHub*.

<!--more-->

The address for the commit is [https://github.com/TryCatchLearn/DatingApp/tree/ead674202f9cde962935b95e508302329f292644](https://github.com/TryCatchLearn/DatingApp/tree/ead674202f9cde962935b95e508302329f292644)

*clone* the project in the regular way then *reset* to the specific commit

```shell
git clone https://github.com/TryCatchLearn/DatingApp.git
cd DatingApp
git reset --hard ead674202f9cde962935b95e508302329f292644
```

To go back the last commit

```shell
git pull
```
