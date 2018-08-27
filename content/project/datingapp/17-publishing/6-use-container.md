---
title: "Use container"
date: 2018-08-14T14:03:00.115+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

## Preparation

Clone the repository

```shell
git clone https://github.com/gabrieleteotino/DatingApp.git
cd DatingApp/DatingApp.API
```

Create the image (this will take a few minutes the first time it is run)

```shell
docker build -t teo/datingapp:runtime .
```

Create an evironment file to store secrets parameters

```shell
cp datingapp_example.env datingapp.env
```

Open **datingapp.env** (it is in gitignore to avoid publishing) and insert the correct values

Run compose (the first run is a bit slow) and detach

```shell
docker-compose up -d
# to force rebuild
docker-compose up -d --build
```

View logs

```shell
docker-compose logs
```

Stop compose

```shell
docker-compose down
# Stop and remove all volumes
docker-compose down -v
```


## App Config

Replace the configuration of the container with the local version.

volumes:
      - ./nginx/index.html:/usr/share/nginx/html/index.html
