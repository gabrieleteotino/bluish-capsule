---
title: "Linux systemd sleep hooks"
date: 2019-12-20T13:26:40+00:00
subtitle: "Hooks to unload and reload lwfinger/rtl8723bu wifi driver"
author: Gabriele Teotino
tags: ["linux"]
categories: ["linux"]
draft: false
---
When I suspend and resume my pc the wifi driver stops working. A simple reload is enough to fix it... so let's automate the unload/load commands

<!--more-->

Create a new service hook

```
sudo vi /etc/systemd/system/root-resume.service
```

Paste this

```
[Unit]
Description=(un)load module 8723bu when going to/from sleep
Before=sleep.target
StopWhenUnneeded=yes

[Service]
Type=oneshot
RemainAfterExit=yes
ExecStart=modprobe -r 8723bu
ExecStop=modprobe 8723bu

[Install]
WantedBy=sleep.target
```

Enable the service so that it starts on boot

```
sudo systemctl enable root-resume.service
```

Credit [Antonio Carlos](https://unix.stackexchange.com/a/519827/387215)
