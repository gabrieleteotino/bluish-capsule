---
title: "Containerize"
date: 2018-08-10T10:33:17.306+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

## Install Docker CE

Official [installation guide](https://docs.docker.com/install/linux/docker-ce/debian/)

```shell
sudo apt update
sudo apt-get install \
     apt-transport-https \
     ca-certificates \
     curl \
     gnupg2 \
     software-properties-common
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
sudo add-apt-repository \
  "deb [arch=amd64] https://download.docker.com/linux/debian \
  $(lsb_release -cs) \
  stable"
sudo apt update
sudo apt install docker-ce
```

Verify the installation by running the **hello-world** container

```shell
sudo docker run hello-world
```

Configure Docker to be run by a non root user

```shell
sudo groupadd docker
sudo usermod -aG docker $USER
```

Log out and log back in, then test running **hello-world** without sudo (on my machine this required a full restart)

```shell
docker run hello-world
```

## Extension

There is a good extension for vscode named *Docker* from *peterjausovec.vscode-docker*

## Dockerfile

The image **microsoft/dotnet:runtime** is for running an application in production. There is also an image for building and testing using Docker, but I will not use that here.

In the root folder of **DatingApp.API** create a new file **Dockerfile**

```shell
FROM microsoft/dotnet:2.1-aspnetcore-runtime
WORKDIR /app
COPY ./bin/Release/netcoreapp2.1/publish/. ./

ENTRYPOINT ["dotnet", "DatingApp.API.dll"]
```

Create the image (this will take a few minutes the first time it is run)

```shell
docker build -t teo/datingapp:runtime .
```

Run the image (this step will fail because there is no MySql)

```shell
docker run -it --rm -p 5001:80 --name datingapp_production teo/datingapp:runtime
```

## Install Docker Compose

To run **DatingApp** we need a database. To orchestrate two Docker containers, one for dotnet and one for MySql, we use Docker Compose.

On Windows and Osx Compose is already installed with Docker.

Official [installation guide](https://docs.docker.com/compose/install/)

Check the latest Compose version on the [compose releases](https://github.com/docker/compose/releases)

```shell
# Download compose (use the latest version)
sudo curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

Test the installation.

```shell
$ docker-compose --version
```

## Prepare the database script

```shell
export ASPNETCORE_ENVIRONMENT=Production
dotnet ef migrations script --output "_MySql_Init_Script/generate_datingapp_db.sql"
```

## Add Compose to DatingApp

Our compose will use a database [container](https://hub.docker.com/_/mysql/) using MySql version 5.7 (I had some problems with version 8)

Then configure the environment of the dotnet container to use a connection string to the new db.

Create a file **docker-compose.yml**

```yaml
version: '3.0'

services:
  datingdb:
    image: mysql:5.7
    environment:
      MYSQL_RANDOM_ROOT_PASSWORD: 1
      MYSQL_DATABASE: datingappschema
      MYSQL_USER: datingappuser
      MYSQL_PASSWORD: password
    volumes:
      - dbdata:/var/lib/mysql
      - ./_MySql_Init_Script:/docker-entrypoint-initdb.d

  datingapp:
    depends_on:
      - datingdb
    image: teo/datingapp:runtime
    environment:
      - ASPNETCORE_ENVIRONMENT=Production
      - ConnectionStrings__DatingDbConnection=Server=datingdb;Database=datingappschema;Uid=datingappuser;Pwd=password;
    env_file: ./datingapp.env
    build:
      context: .
    ports:
      - '8080:80'

volumes:
  dbdata:
```

Create an evironment file to store secrets parameters

```shell
cp datingapp_example.env datingapp.env
```

Open **datingapp.env** (it is in gitignore to avoid publishing) and insert the correct values

```
# JWT toke secret
AppSettings__TokenSecret="tokensecretforproduction"

# Cloudinary
CloudinarySettings__CloudName="name"
CloudinarySettings__ApiKey="key"
CloudinarySettings__ApiSecret="secret"
```

Run compose (the first run is a bit slow)

```shell
docker-compose up
# to force rebuild
docker-compose up --build
```

Stop compose

```shell
docker-compose down
# Stop and remove all volumes
docker-compose down -v
```
