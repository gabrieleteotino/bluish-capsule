---
title: "Remove LibreOffice"
date: 2018-03-13T11:37:55+01:00
subtitle: ""
author: Gabriele Teotino
tags: ["linux"]
categories: ["sysop"]
draft: false
---

To free up some space I removed LibreOffice and obtained about 720M of space.

```shell
sudo apt-get remove --purge libreoffice*
sudo apt-get clean
sudo apt-get autoremove
```
