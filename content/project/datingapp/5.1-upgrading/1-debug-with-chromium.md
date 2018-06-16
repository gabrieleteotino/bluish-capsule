---
title: "Debug with Chromium"
date: 16-06-2018T14:27:09+02:00
subtitle: ""
author: Gabriele Teotino
tags: []
categories: []
draft: true
---

In *vscode* go to the *debug* panel and click on the cog in the top right.

Change the content of **launch.json** to

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "chrome",
            "request": "launch",
            "name": "Launch Chromium",
            "url": "http://localhost:4200",
            "runtimeExecutable": "/usr/bin/chromium",
            "webRoot": "${workspaceFolder}",
            "sourceMaps": true,
            // this enables chromium to be launched even if it is alredy open
            "userDataDir": "${workspaceRoot}/.vscode/chrome",
        }
    ]
}
```

Be sure to have installed the extension **Debugger for Chrome**.
