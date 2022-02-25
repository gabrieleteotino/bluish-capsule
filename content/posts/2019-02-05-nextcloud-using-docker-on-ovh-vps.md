---
title: "Nextcloud using docker on ovh vps"
date: 2019-02-05T13:01:25.491+01:00
subtitle: "Installing Nexctcloud personal cloud server using docker on a virtual private server hosted by OVH"
author: Gabriele Teotino
tags: ["nextcloud", "cloud", "docker", "debian", "linux", "ovh"]
categories: ["cloud"]
draft: false
---

[Nextcloud](https://nextcloud.com/) is a private cloud service platform. I plan to use it for personal and family usage, just to move some of my personal data away from third parties.

<!--more-->

Official build for docker [source](https://github.com/nextcloud/docker) and [dockerhub](https://hub.docker.com/_/nextcloud/).

There is a good starting example in github (the postgres version has an open bug and the workaround is not worth my time).

```shell
mkdir nextcloud
cd nextcloud
mkdir git
cd git
git clone https://github.com/nextcloud/docker.git
cp docker/.examples/docker-compose/with-nginx-proxy/mariadb/apache/* ../ -r
cd ..
```

Now in the folder *~/nextcloud* we have the git clone and the directory. It is possible to remove the git folder.

```shell
rm -r git
```

Edit **db.env** and put a new strong password for the database user

Edit **docker-compose.yml** and

- add a strong password for *MYSQL_ROOT_PASSWORD=*
- insert your nextcloud domain behind *VIRTUAL_HOST=* and *LETSENCRYPT_HOST=* (yes the same cloud.domain.tld in both fields)
- enter a valid email behind *LETSENCRYPT_EMAIL=*

Build the images

```shell
docker-compose build --pull
```

Run

```shell
docker-compose up -d
```

Open the browser to the address specified in *VIRTUAL_HOST=* and create an admin user.

## Post install

### Bigint fix

I used portainer to execute a update command ([bigint identifiers](https://docs.nextcloud.com/server/15/admin_manual/configuration_database/bigint_identifiers.html)).

In portainer select the container **nextcloud_app_1** (or whatever the name/number is for your installation) click on **>_** to execute a console command

```shell
command: php occ db:convert-filecache-bigint
user: www-data
```

Execution may take some time and I really don't understand why this was not alredy the default.

### Email SMTP

I registered a free Mailgun account without any domain. Before you can use the SMTP go into the default domain that mailgun generated and scroll down to **Authorized Recipients** and add the email address for your administrator and for your users. This can be done for up to 5 email. Eventually you can subscribe to the pay as you go plan that is also free.

Mailgun will send a mail to each authorized recipient, open the message and follow the instruction to enable the address.

From Nextcloud administration in the section *basic settings*

- Send mode: SMTP
- Encryption: STARTTLS
- From address: postmaster
- @: sandboxceXXXXXXXXXXXXXXXXX
- Authentication method: Login
- Authentication required: yes
- Server address: smtp.mailgun.org
- Port: 2525
- Credential: postmaster@sandboxceXXXXXXXXXXXXXXXXX.mailgun.org
- Password

Click the button to send a test mail and if all is good a mail will arrive in a few seconds.
