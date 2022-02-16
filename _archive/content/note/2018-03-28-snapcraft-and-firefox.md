---
title: "Snap and Firefox"
date: 2018-03-28T14:43:13+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["linux", "debian"]
categories: ["tools"]
draft: false
---

[Snapcraft](https://snapcraft.io/) is a tool to install containerized software packages.

Now Firefox is available!

<!--more-->
Installing Firefox (finally not ESR) in Debian
```shell
sudo apt install snapd
sudo snap install firefox
snap run firefox
```

A few small problems

- about 100Mb more space required
- unable to use themes
- other users report problems with netflix (amazon prime video works)
