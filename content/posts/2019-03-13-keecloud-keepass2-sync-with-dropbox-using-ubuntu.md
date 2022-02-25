---
title: "KeeCloud on Keepass2 to sync with Dropbox using Ubuntu"
date: 2019-03-13T16:32:20+01:00
subtitle: ""
author: Gabriele Teotino
tags: ["keepass", "dropbox", "ubuntu", "linux"]
categories: ["linux"]
draft: false
---

Installation and configuration of Keecloud a Keepass2 plugin that can open and synchronize a file from Dropbox.

<!--more-->

Before installing any keepass2 plugin we need the full **mono** installation

```shell
sudo apt install mono-complete
```

The other available plugin **KeeAnywhere** is currently NOT working with mono.

## KeeCloud installation

Get the latest **plgx** from the official [download page](https://bitbucket.org/devinmartin/keecloud/downloads/)

```shell
wget https://bitbucket.org/devinmartin/keecloud/downloads/KeeCloud.1.2.1.11.plgx
sudo mv KeeCloud.1.2.1.11.plgx /usr/lib/keepass2/Plugins/
sudo chown root:root /usr/lib/keepass2/Plugins/KeeCloud.1.2.1.11.plgx

```

## Credentials

Open Keepass then from the **Tools** menu select **URL Credential Wizard**

Select Dropbox as a provider then a new browser will launch asking to *"Sign in to Dropbox to link with KeeCloud"* enter your Dropbox credentials then allow access.

A code will appear, copy and paste it in the **Credential Configuration** window.

Click **Next** and in the new window click **Show Password** copy the new password for the next step (this one is different from the previous one).

## Opening a file

From the **File** menu select **Open -> Open URL**

Enter the url for your file in the form

```shell
dropbox://pathtoyourfile.kdbx
```

Leave the username empty and paste the password from the previous step. Select *Remember user name and Password*.

Click OK and voilà.
