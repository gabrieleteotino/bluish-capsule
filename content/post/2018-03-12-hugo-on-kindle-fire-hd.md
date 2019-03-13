---
title: "Hugo on Kindle Fire Hd"
date: 2018-03-12T13:43:50+01:00
subtitle: "Using termux on a Kindle Fire HD to manage a hugo site"
author: Gabriele Teotino
tags: ["hugo", "android", "termux", "kindle-fire-hd"]
categories: ["webdev"]
draft: false
---
On a Kindle Fire HD we are going to install everything we need to update our hugo site.

## Install termux

> [Termux](https://termux.com/) is an Android terminal emulator and Linux environment app that works directly with no rooting or setup required. A minimal base system is installed automatically - additional packages are available using the APT package manager.

<!--more-->

It is available on the [Play Store](https://play.google.com/store/apps/details?id=com.termux) and on [F-Droid](https://f-droid.org/repository/browse/?fdid=com.termux).

Install and start the application.

### Tips

- Keep pressed `Volume-Up` plus `q` on the keyboard to activate a special row on the keyboard.
- Keep pressed `Volume-Up` plus `w` to emulate the Up arrow key

## Install git

Update the packages
```shell
apt update
apt upgrade
```

Install git and OpenSSH
```shell
apt install git
apt install openssh
```

## External storage

Termux store the HOME folder in the application sandbox which is only accessible to the app itself. This is good: only a phone root user can access your private configuration file like ssh keys.

To be able to edit our website from an external application we have to mount an external storage inside our HOME folder.

```shell
termux-setup-storage
cd storage
ls -la
```

We see that the command have setup for us a few symlinks to the most used folders: `dcim`, `downloads`, `movies`, `music`, `pictures`, `shared`.

## Github ssh keys

### Generate keys

Similar to the process specified in this [article]({{< relref "/post/2018-02-23-ssh-keys-for-github.md" >}})

```shell
ssh-keygen -t rsa -b 4096 -C "gabriele@teosoft.it"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
```

Now we need something to copy the value of the generated key to the clipboard.

### Termux:API

Install Termux:API from [Play Store](https://play.google.com/store/apps/details?id=com.termux.api) or [F-Droid](https://f-droid.org/packages/com.termux.api/)

Now enable the api support inside termux
```shell
apt install termux-api
```

### Activate Github key

Copy the ssh public key value to the clipboard
```shell
cat ./.ssh/id_rsa.pub | termux-clipboard-set
```

Open a browser and log in to GitHub.

Go into your profile and select [SSH and GPG keys](https://github.com/settings/keys), click **Add SSH key**, add a description and paste the key.

## Clone the repository

The repository will be cloned in the shared folder. This way it will be possible for other application to edit the content files.

```shell
cd storage/shared
git clone --recurse-submodules -j8 git@github.com:gabrieleteotino/bluish-capsule.git
cd bluish-capsule
ls -la
```

## Install hugo
We will install a prebuilt release (it is also possible to build from the sources).

Go to the [hugo releases](https://github.com/gohugoio/hugo/releases) and find the url for the linux-ARM.tar.gz

Note: the deb package is in *armhf* and is not installable because my system support only arm.

Note: wget is not available with apt in Termux.

```shell
# install wget
pkg install wget
# move back to home
cd
# download the file
wget https://github.com/gohugoio/hugo/releases/download/v0.37.1/hugo_0.37.1_Linux-ARM.tar.gz
# extract
tar xzf hugo_0.37.1_Linux-ARM.tar.gz
```

Install the binary into a personal `bin` folder. Easy to manage and upgrade.

```shell
mkdir -p ~/bin
cd ~
mv hugo ~/bin/hugo
cd ~/bin
./hugo version
```

Add bin path
```shell
vim ~/.bash_profile
```
Add the following line
`export PATH=$PATH:$HOME/bin`

Open a new terminal session ( or `. ~/.bash_profile`) and test
```shell
which hugo
hugo version
```

## Run the site

```shell
cd ~/storage/shared/bluish-capsule
hugo server -D
```

This page was edited and published from my kindle. A bit painful but doable.
