---
title: "Linux screen"
date: 2018-03-29T11:04:15+02:00
subtitle: "Using screen command to mantain a remote session open between ssh sessions for long running tasks with multiple shell windows"
author: Gabriele Teotino
tags: ["debian", "tools"]
categories: ["linux"]
draft: true
---

Install and run

```shell
apt install screen
screen
```
<!--more-->
To send a command press **Ctrl+A** then the command character. Use **?** for help.


## Multiple windows, detaching and reattaching

```shell
# Launch screen
screen
# start top
top
# Create a new window Ctrl+a c
# Launch a command in the new window
echo "this is window 2"
# Switch back to the window with top Ctrl-a n
# Detach Ctrl-a d
```

Now screen is running with the two windows in background. It is possible to close the ssh session and reopen a new one.
```shell
# Reconnect to screen
screen -r
```

It is possible to run multiple sessions of screen in parallel. To reattach we need the session id.
```shell
screen -ls
There are screens on:
	20097.pts-2.zap-bp	(03/29/2018 02:51:53 PM)	(Detached)
	19566.pts-2.zap-bp	(03/29/2018 02:42:50 PM)	(Detached)
2 Sockets in /run/screen/S-zap.

screen -r 19566
```

## Logging and locking

Log all commands in a screen session using **Ctrl+a H**

Take a screenshot **Ctrl+a h**

Lock the screen **Ctrl+a x**

## Windows and panels

Ctrl+a S Horizontal split

Ctrl+a | Vertical split

Ctrl+a tab Cycle panels

Ctrl+a c Create a window in the current panel

Ctrl+a n Select the next window in the current panel

Ctrl+a " List windows
