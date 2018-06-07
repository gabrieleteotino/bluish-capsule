---
title: "Using Ansible to setup my machine"
date: 2018-06-05T10:55:56+02:00
subtitle: "With an ansible script setup all the software for my dev workstation"
author: Gabriele Teotino
tags: ["ansible", "linux", "ubuntu", "vscode", "postman"]
categories: ["devops"]
draft: true
---

The deployment of the software will be made with [ansible-pull](https://docs.ansible.com/ansible/2.4/ansible-pull.html), without a remote machine. This is a little different from the classic client server deployment used with ansible and is in a way simpler.

To use ansible-pull we have to [install ansible](https://docs.ansible.com/ansible/2.4/intro_installation.html) on the machine and download all the playbooks from a git repository.

<!--more-->

This installation was tested on a clean virtual machine with Xubuntu 18.04.

# Usage

## Prerequisite

 Install ansible using pip.m Alternative methods are available in the [installation guide](http://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html)

```shell
sudo apt install git
sudo apt install python-pip
sudo pip install ansible
```

## Run

```shell
ansible-pull -U https://github.com/gabrieleteotino/ansible-workstation.git -K
```



# Creation

## Git

Create a repository in github.

Clone the repository
git clone git@github.com:gabrieleteotino/ansible-workstation.git

## Hosts inventory

Create `ansible.cfg`

```
[defaults]
inventory = inventory
```

Create `inventory`

```
[localhost]
127.0.0.1 ansible_connection=local
```

This default configuration just specify localhost. It is possible to add customizations for a single machine.


more nice roles

https://github.com/gantsign/ansible-role-postman
https://github.com/gantsign/ansible-role-atom

https://github.com/gantsign/ansible-role-zram-config
https://github.com/gantsign/ansible-role-xdesktop
https://github.com/gantsign/ansible-role-dockbarx-launcher
https://github.com/gantsign/ansible-role-default-web-browser


## Galaxy
some stuff is already made

1. create a `requirements.yml`
2. run `ansible-galaxy install -r requirements.yml`

Example requirements file:

```yaml
- src: https://github.com/staylorx/ansible-role-wls-prep.git
  version: master
  name: staylorx.wls-prep

- src: https://my-work-git-extravaganza.com
  version: 2.x
  name: coolplace.niftyrole

#From Ansible Galaxy
- src: staylorx.oracle-jdk
```

A playbook to install roles: `install-roles.yml`.

```yaml
---

- hosts: localhost

  tasks:
    - file:
        path:  roles
        state: absent

    - local_action:
        command ansible-galaxy install -r requirements.yml --roles-path roles

    - lineinfile:
        dest:   .gitignore
        regexp: '^\/roles$'
        line:   '/roles'
        state:  present
```

A task from ferrarimarco to install roles from Galaxy

```yaml
---
- name: Ensure Ansible roles are installed
  shell: ansible-galaxy list | grep {{ item }}
  register: ansible_roles_list
  with_items: "{{ ansible_roles_to_install }}"
  # grep will exit with 1 when no results found.
  # This causes the task not to halt play.
  failed_when: "ansible_roles_list.rc > 1"
  changed_when: false

- name: Install roles from Ansible Galaxy
  command: ansible-galaxy install {{ item.item }}
  register: ansible_galaxy_install_result
  with_items:
    - "{{ ansible_roles_list.results }}"
  changed_when: "{{ ansible_galaxy_install_result.rc }} == 1"
```

Sample playbook:

```yaml
- hosts: all
    roles:
      - { role: ferrarimarco.install-roles, become: yes, ansible_roles_to_install: ['geerlingguy.java', 'geerlingguy.nginx'] }
```
