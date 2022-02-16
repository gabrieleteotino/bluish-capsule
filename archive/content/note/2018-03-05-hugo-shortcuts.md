---
title: "Hugo Shortcuts"
date: 2018-03-05T14:33:07+01:00
subtitle: ""
tags: ["hugo"]
categories: ["webdev"]
draft: false
---

Some useful commands for Hugo.

<!--more-->

Command line tool
```shell
# build site
hugo

# Run site in localhost with drafts
hugo server -D

# Create new site
hugo new site

# Create new content
hugo new path/to/file.md

# List content
hugo list drafts
hugo list expired
hugo list future

# Garbage Collection, removes generate resources like resized images
hugo --gc
```
---

For *draft*, *expired* or *future* status in the *front matter* specify:
```yaml
draft: true
# if true, the content will not be rendered unless the --buildDrafts flag is passed to the hugo command.

expiryDate: 2018-05-04T12:30+01:00
# the datetime at which the content should no longer be published by Hugo; expired content will not be rendered unless the --buildExpired flag is passed to the hugo command.

publishDate: 2018-05-04T12:30+01:00
# if in the future, content will not be rendered unless the --buildFuture flag is passed to hugo.
```
