---
title: "Multiple SSH Keys"
date: 2018-05-02T15:34:57+02:00
subtitle: "Managing multiple SSH keys for Github and Visual Studio Online"
author: Gabriele Teotino
tags: ["ssh", "git", "github", "visual studio online"]
categories: ["development"]
draft: false
---

After generating a new key for my Visual Studio Online code repository I have to tell git which key to use for each connection.

Create a file in `~/.ssh/config`

Add the following content
```
# Uncomment to enable debugging.
# Levels are: QUIET, FATAL, ERROR, INFO, VERBOSE, DEBUG, DEBUG1, DEBUG2, and DEBUG3.
# LogLevel DEBUG

Host vs-ssh.visualstudio.com
    IdentityFile ~/.ssh/id_rsa_visualstudio

Host github.com
    IdentityFile ~/.ssh/id_rsa_github

# tell ssh-agent to not try keys
Host *
    IdentitiesOnly yes

```
