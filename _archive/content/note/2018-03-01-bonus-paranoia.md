---
title: "Bonus Paranoia"
date: 2018-03-01T12:50:13+01:00
subtitle: ""
tags: ["linux", "privacy"]
categories: []
draft: false
---

If you're feeling especially paranoid there are various things you could do to reduce logging on linux.

<!--more-->
A simple and particularly drastic option is:
```shell
sudo rm /var/log/syslog && sudo ln -s /dev/null /var/log/syslog
sudo rm /var/log/auth.log && sudo ln -s /dev/null /var/log/auth.log
```
