---
title: "Angular"
date: 2018-05-30T22:03:22+02:00
subtitle: ""
author: Gabriele Teotino
tags: []
categories: []
draft: true
---

Create a new Angular application inside the DatingApp folder, at the same level of the DatingApp.API.

```shell
cd ~/dev/DatingApp
ng new DatingApp.SPA
```

In the *package.json* file we can see that the default scripts for npm are already configured to launch ng with the appropriate parameters.

Let's run the default Angular application and check that everything is working

```shell
npm start
# equivalent to
# ng serve
```

Navigate with the browser to http://localhost:4200/

Now it is a good time to make a git commit with new files.
