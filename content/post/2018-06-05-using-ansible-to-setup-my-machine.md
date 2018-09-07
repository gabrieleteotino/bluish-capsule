---
title: "Using Ansible to setup my machine"
date: 2018-06-05T10:55:56+02:00
subtitle: "With an ansible script setup all the software for my dev workstation"
author: Gabriele Teotino
tags: ["ansible", "linux", "ubuntu", "vscode", "postman"]
categories: ["devops"]
draft: false
---

The deployment of the software will be made with [ansible-pull](https://docs.ansible.com/ansible/2.4/ansible-pull.html), without a remote machine. This is a little different from the classic client server deployment used with ansible and is, in a way, simpler.

<!--more-->

To use ansible-pull we have to [install ansible](https://docs.ansible.com/ansible/2.4/intro_installation.html) on the machine and download all the playbooks from a git repository.

This installation was tested on a clean virtual machine with Xubuntu 18.04.

# Usage

## Installation

 Install ansible using pip. Alternative methods are available in the [installation guide](http://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)

```shell
sudo apt install git
sudo apt install python-pip
sudo pip install ansible
```

## Run

```shell
ansible-pull -U https://github.com/gabrieleteotino/ansible-workstation.git -K
```

## More examples
[Gantsign](https://github.com/gantsign) has a lot of nice roles to use directly or take inspiration.

https://github.com/gantsign/ansible-role-postman
https://github.com/gantsign/ansible-role-atom

https://github.com/gantsign/ansible-role-zram-config
https://github.com/gantsign/ansible-role-xdesktop
https://github.com/gantsign/ansible-role-dockbarx-launcher
https://github.com/gantsign/ansible-role-default-web-browser
