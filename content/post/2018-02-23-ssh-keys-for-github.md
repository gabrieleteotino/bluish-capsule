---
title: "SSH Keys for Github"
date: 2018-02-23T11:13:18+01:00
subtitle: "How to authenticate to github using SSH keys"
tags: ["github"]
categories: ["development"]
draft: true
---

The complete instruction on [Github](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)

Create a key in the default location without any passphrase (or add one if you like). Then add it to the ssh-agent.

```shell
ssh-keygen -t rsa -b 4096 -C "gabriele@teosoft.it"
ssh-add ~/.ssh/id_rsa
```

Copy the key into the clipboard
```shell
cat ~/.ssh/id_rsa.pub
```

Go into your profile and select [SSH and GPG keys](https://github.com/settings/keys), click **Add SSH key**, add a description and paste the key.
