---
title: ".Net Core Marten and Postgres"
date: 2018-03-20T13:06:00+01:00
subtitle: ""
author: Gabriele Teotino
tags: ["postgresql", "c#", "netcore"]
categories: ["development"]
draft: false
---


## Install and configure postgresql

```shell
sudo apt install postgresql-9.6
  ...
  Creating new cluster 9.6/main ...
    config /etc/postgresql/9.6/main
    data   /var/lib/postgresql/9.6/main
    locale en_US.UTF-8
    socket /var/run/postgresql
    port   5432
  ...
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

Create two users in postgresql: one equal to the current interactive user for pgAdmin3 the other for marten.

```shell
sudo -u postgres -i
# this must be same as the current interactive user
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

Create a folder `Models` and add a class `User.cs`
```c#
using System;

namespace Models
{
    public class User
    {
        public Guid Id { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public bool Internal { get; set; }
        public string UserName { get; set; }
    }
}
```

Modify the `Main` method of `Program.cs`

```c#
static void Main(string[] args)
{
    var store = DocumentStore
        .For("host=localhost;database=marten_db;password=marten_password;username=marten_user");

    using (var session = store.LightweightSession())
    {
        var user = new User { FirstName = "Han", LastName = "Solo" };
        session.Store(user);

        session.SaveChanges();
        Console.WriteLine("User saved");
    }

    using (var session = store.OpenSession())
    {
        var existingUser = session
            .Query<User>()
            .Where(x => x.FirstName == "Han" && x.LastName == "Solo")
            .Single();
        Console.WriteLine($"Found {existingUser.FirstName} {existingUser.LastName}");
    }

    Console.WriteLine("Done!");
}
```

Running the application this program will create a table and some functions for the *User* class, add a row and load it back.
```shell
dotnet run
```

Note the four functions and the table *mt_doc_user*

![Marten generated tables](screenshot-marten-tables-pgadmin3.png)
