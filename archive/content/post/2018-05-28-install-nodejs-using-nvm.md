---
title: "Install nodejs using nvm"
date: 2018-05-28T19:17:51+02:00
subtitle: "Installation of node on ubuntu using node version manager"
author: Gabriele Teotino
tags: ["linux", "nodejs", "node", "nvm", "ubuntu"]
categories: ["dev"]
draft: false
---

[Node Version Manager](https://github.com/creationix/nvm) is Simple bash script to manage multiple active node.js versions.

<!--more-->

Installation of nvm is a single command

```shell
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
# reload the terminal or execute
source .bashrc
```

List installed node versions
```
nvm ls
```

List available node versions
```
nvm ls-remote
```

Install the last versions
```
nvm install node
```

Install the last LTS version
```
nvm install --lts
```
