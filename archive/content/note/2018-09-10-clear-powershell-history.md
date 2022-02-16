---
title: "Clean PowerShell history"
date: 2018-09-10T17:07:03.657+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["powershell"]
categories: ["command line"]
draft: false
---

- Clear Windows 10 (Server 2016) history "feature"
```Remove-Item (Get-PSReadlineOption).HistorySavePath```
- Reset the console buffer
```ALT+F7```
- Clear PSReadline's session history
```[Microsoft.PowerShell.PSConsoleReadLine]::ClearHistory()```
- Clear PowerShell's own history
```Clear-History```

Commands taken from this [stackoverflow article](https://stackoverflow.com/questions/13257775/powershell-clear-history-doesnt-clear-history)
