---
title: "Linux tmux"
date: 2018-03-29T15:34:35+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["debian", "tools"]
categories: ["linux"]
draft: true
---

Install and run

```shell
sudo apt install tmux
```

Ctrl+b ? show help

## Panels

Ctrl+b % vertical split

Ctrl+b " Horizontal split

Ctrl-b <arrow key> change panel

Ctrl-d or exit close a panel

## Windows

Ctrl-b c create a window

Ctrl-b n next window

Ctrl-b p previous window

## Detach and reattach
Ctrl-b d detach the current session

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

Ctrl-b z make a panel go full screen and back to normal size

Ctrl-b , rename the current window

Ctrl-b Ctrl-<arrow key> resize the panel in the specified direction
