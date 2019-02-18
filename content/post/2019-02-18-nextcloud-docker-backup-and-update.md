---
title: "Nextcloud using docker backup and update"
date: 2019-02-18T14:35:40.202+01:00
subtitle: "Backup and update a Nexctcloud personal cloud server using docker"
author: Gabriele Teotino
tags: ["nextcloud", "cloud", "docker", "debian", "linux"]
categories: ["cloud"]
draft: false
---

Backup and update an instance of Nextcloud installed via docker compose.

<!--more-->

## Backup

The volumes for my Nextcloud installation are

- nextcloud_db (242Mb)
- nextcloud_nextcloud (222Mb)
- nextcloud_certs
- nextcloud_html
- nextcloud_vhost.d

The Nextcloud instance is just installed and there is almost no user data.

To backup we need to stop the server and leave all the volumes

```shell
cd ~/nextcloud
docker-compose down
```

Now the volumes are all *unused*.

The generic template to backup a volume is

```shell
docker run --rm \
  --volume [DOCKER_COMPOSE_PREFIX]_[VOLUME_NAME]:/[TEMPORARY_DIRECTORY_TO_STORE_VOLUME_DATA] \
  --volume $(pwd):/[TEMPORARY_DIRECTORY_TO_STORE_BACKUP_FILE] \
  ubuntu \
  tar cvf /[TEMPORARY_DIRECTORY_TO_STORE_BACKUP_FILE]/[BACKUP_FILENAME].tar /[TEMPORARY_DIRECTORY_TO_STORE_VOLUME_DATA]
```

Prepare a folder to store the backups

```shell
cd ~/nextcloud
mkdir backup
cd backup
```

Archieve the data in compressed format

```shell
docker run --rm --volume nextcloud_db:/db --volume $(pwd):/backup ubuntu tar cvfz /backup/db.tar.gz /db
docker run --rm --volume nextcloud_nextcloud:/nextcloud --volume $(pwd):/backup ubuntu tar cvfz /backup/nextcloud.tar.gz /nextcloud
docker run --rm --volume nextcloud_certs:/certs --volume $(pwd):/backup ubuntu tar cvfz /backup/certs.tar.gz /certs
docker run --rm --volume nextcloud_html:/html --volume $(pwd):/backup ubuntu tar cvfz /backup/html.tar.gz /html
docker run --rm --volume nextcloud_vhost.d:/vhostd --volume $(pwd):/backup ubuntu tar cvfz /backup/vhostd.tar.gz /vhostd
```

Archieve the data in uncompressed format

```shell
docker run --rm --volume nextcloud_db:/db --volume $(pwd):/backup ubuntu tar cvf /backup/db.tar /db
docker run --rm --volume nextcloud_nextcloud:/nextcloud --volume $(pwd):/backup ubuntu tar cvf /backup/nextcloud.tar /nextcloud
docker run --rm --volume nextcloud_certs:/certs --volume $(pwd):/backup ubuntu tar cvf /backup/certs.tar /certs
docker run --rm --volume nextcloud_html:/html --volume $(pwd):/backup ubuntu tar cvf /backup/html.tar /html
docker run --rm --volume nextcloud_vhost.d:/vhostd --volume $(pwd):/backup ubuntu tar cvf /backup/vhostd.tar /vhostd
```

## Restore

To restore the data create a volume in a new container then restore the content. These are only the schema commands to rebuild and restore a volume.

```shell
docker run -v /dbdata --name dbstore2 ubuntu /bin/bash
docker run --rm --volumes-from dbstore2 -v $(pwd):/backup ubuntu bash -c "cd /dbdata && tar xvf /backup/backup.tar --strip 1"
```

## Upgrade Nextcloud

Easy: let compose do the work

```shell
docker-compose pull
docker-compose up -d
```
