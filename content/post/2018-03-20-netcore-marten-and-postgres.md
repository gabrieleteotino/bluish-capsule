---
title: ".Net Core Marten and Postgres"
date: 2018-03-20T13:06:00+01:00
subtitle: ""
author: Gabriele Teotino
tags: ["postgresql", "c#", "netcore"]
categories: ["development"]
draft: true
---


## Install and configure postgresql

```shell
sudo apt install postgresql-9.6
```

Results

```
Creating new cluster 9.6/main ...
  config /etc/postgresql/9.6/main
  data   /var/lib/postgresql/9.6/main
  locale en_US.UTF-8
  socket /var/run/postgresql
  port   5432
```

Test if the database is working

```shell
sudo su - postgres
postgres$ psql
  psql (9.6.4)
  Type "help" for help.
postgres=# \q
exit
```

Create two users in postgresql, one equal to the current user name for pgAdmin3 the other for marten.

```shell
sudo -u postgres -i
# this must be same as the current user
createuser --interactive zap
  Shall the new role be a superuser? (y/n) y
# for this user we set a password
createuser --interactive marten_user -P
  Enter password for new role:
  Enter it again:
  Shall the new role be a superuser? (y/n) y
```

Install pgAdmin III
```shell
sudo apt install pgadmin3
```

Launch from *Application* -> *Development* -> *pgAdmin III*

To connect to local leave the host empty, use your current interactive user and leave the password blank.

![Connect to Postgresql dialog](screenshot_connect_pgadmin3.png)

To create a new database rigth click on *Databases* -> *New Database...*, name the new database *marten_db*, set *marten_user* as the *Owner* and click ok.

## .net core Application
Create a command line Application
```shell
dotnet new console -o martenApp
cd martenApp/
# add Marten fron NuGet
dotnet add package Marten
# test
dotnet run
```
