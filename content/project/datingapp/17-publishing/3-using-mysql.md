---
title: "Using MySql"
date: 2018-08-07T14:12:55.018+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore"]
categories: ["dev"]
draft: false
---

<!--more-->

As a client admin interface I use [mysql-workbench](https://dev.mysql.com/downloads/workbench/).

Obviously you need to have MariaDb or MySql installed.

## Create MariaDb (MySql) user and DB

Create a database for DatingApp (do not use password as a password)

```shell
sudo su - root
mysql
CREATE USER 'datingappuser'@'localhost' IDENTIFIED BY 'password';
CREATE DATABASE `datingappdb`;
# Grant the user the privilege to access mariadb without any other privilege
GRANT USAGE ON *.* TO 'datingappuser'@localhost IDENTIFIED BY 'password';
# Grant the user all privileges on the application db
GRANT ALL PRIVILEGES ON `datingappdb`.* TO 'datingappuser'@'localhost' WITH GRANT OPTION;
FLUSH PRIVILEGES;
SHOW GRANTS FOR 'datingappuser'@localhost;
```

## EF DB provider

On the page of the provider [nuget package](https://www.nuget.org/packages/Pomelo.EntityFrameworkCore.MySql) verify the version. At the time of writing it was *2.1.1*, so we need to update (or downgrade) dotnet core to *SDK 2.1.301*. Unfortunately there is no way to do that with the package manager.

```shell
sudo apt-get install dotnet-sdk-2.1
```

It installed *2.1.302* and ef version is *2.1.1-rtm-30846* so I hope the provider will work.

Open **DatingApp.API.csproj** and update the reference version for EF Sqlite

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite" Version="2.1.1"/>
```

Restore

```shell
dotnet restore
```

Now install the provider for MySql

```shell
dotnet add package Pomelo.EntityFrameworkCore.MySql --version 2.1.1
dotnet restore
```

Open **Startup.cs** and duplicate the method **ConfigureServices** and name the copy as **ConfigureDevelopmentServices**. Those two methods will be called by convention based on the environment.

Now we can change **ConfigureServices** to use *MySql* and still mantain *Sqlite* for development

```csharp
services.AddDbContext<DatingContext>(options => options.UseMySql(Configuration.GetConnectionString("DatingDbConnection")));
```

Move the connection string for *Sqlite* from **appsettings.json** to **appsettings.Development.json**

```json
{
  "ConnectionStrings": {
    "DatingDbConnection": "Data Source=DatingApp.db;"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    }
  }
}
```

In **appsettings.json** change the connection string to *MySql*

```json
"DatingDbConnection": "Server=localhost; Database=datingappdb; Uid=datingappuser; Pwd=password;"
```

There is some convoluted way to keep the migrations for both databases. To keep things simple I just moved the **Migrations** folder out of the solution. Now when we create a new migration *ef* will start from zero.

```shell
dotnet ef migrations add Initial
dotnet ef database update
```
