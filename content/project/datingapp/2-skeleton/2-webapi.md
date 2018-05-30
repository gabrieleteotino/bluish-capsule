---
title: "Datingapp Skeleton"
date: 2018-05-29T16:49:07+02:00
subtitle: "A skeleton api for the dating app"
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore"]
categories: ["dev"]
draft: true
---

Create a simple skeleton with a webapi that loads data from the database.

<!--more-->

## Github setup
Create a folder
```
mkdir DatingApp
cd DatingApp
```

## Webapi setup

Create a new api project
```
dotnet new webapi -o DatingApp.API
cd DatingApp.API
```

Start the application
```
dotnet run
```

Check with the browser that the api is running navigating to (https://localhost:5001/api/values)[https://localhost:5001/api/values].

The course suggest to add *dotnet watch* to the project. This step is no more needed with the new relase of netcore.

Run with watch and test a small change on the value controller to see that everithing is working
```
dotnet watch run
```

## Webapi database

Create a `Models` folder inside the project.

Right click on the folder and **New C# class** and name it `Value`.

```c#
namespace DatingApp.API.Models
{
    public class Value
    {
        public int Id { get; set; }
        public string Name { get; set; }
    }
}
```

Create a `Data` folder inside the project.

Right click on the folder and **New C# class** and name it `DatingContext`.

```c#
using Microsoft.EntityFrameworkCore;
using DatingApp.API.Models;

namespace DatingApp.API.Data
{
    public class DatingContext : DbContext
    {
        public DatingContext(DbContextOptions<DatingContext> options)
            : base(options)
        { }

        public DbSet<Value> Values { get; set; }
    }
}
```

In the terminal
- Stop dotnet watch
- `dotnet add package Microsoft.EntityFrameworkCore.Sqlite -v 2.1.0-rc1-final`


Open **appsettings.json** and add a new section

```json
  "ConnectionStrings": {
    "DatingDbConnection": "Data Source=DatingApp.db"
  },
```


Register the dbcontext in `Startup.cs` **ConfigureServices** method.

```c#
...
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    services.AddDbContext<DatingContext>(options => options.UseSqlite("connectionname"));
}
...
```

Create an initial migration with EF

```shell
dotnet ef migrations add InitialMigration
dotnet ef database update
```

This command created a **Migrations** folder with 3 files.
