---
title: "Linux tmux"
date: 2018-03-29T15:34:35+02:00
subtitle: "Using tmux command to mantain a remote session open between ssh sessions for long running tasks with multiple shell windows"
author: Gabriele Teotino
tags: ["debian", "tools"]
categories: ["linux"]
draft: false
---

Install and run

```shell
sudo apt install tmux
```

<!--more-->
To send a command press **Ctrl+b** then the command character. Use **?** for help.

Ctrl+b ? show help

## Panels

**Ctrl+b %** vertical split

**Ctrl+b "** horizontal split

**Ctrl-b &lt;arrow key&gt;** change panel

**Ctrl-b Ctrl-&lt;arrow key&gt;** resize the panel in the specified direction

**Ctrl-d** or exit close a panel

## Windows

**Ctrl-b c** create a window

**Ctrl-b n** next window

**Ctrl-b p** previous window

## Detach and reattach
**Ctrl-b d** detach the current session

Reattach

```shell
# List existing sessions
tmux ls
# Attach to session 0
tmux attach -t 0
```

## Session names

```shell
# create a new named session
tmux new -s database
# rename an existing session
tmux rename-session -t 0 database
# connect to named session
tmux attach -t database
```

## Other nice commands

**Ctrl-b z** make a panel go full screen and back to normal size

**Ctrl-b ,** rename the current window
