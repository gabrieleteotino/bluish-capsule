---
title: "Webapi skeleton"
date: 2018-05-29T16:49:07+02:00
subtitle: "A skeleton api for the dating app"
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore"]
categories: ["dev"]
draft: true
---

A skeleton webapi that loads data from asqlite database.

<!--more-->

## Webapi setup

Create a new api project
```shell
cd ~/dev/DatingApp
dotnet new webapi -o DatingApp.API
cd DatingApp.API
```

Start the application
```shell
dotnet run
```

Check with the browser that the api is running navigating to [https://localhost:5001/api/values](https://localhost:5001/api/values).

Add a `.gitignore` file

```
*.swp
*.*~
project.lock.json
.DS_Store
*.pyc

# Visual Studio Code
.vscode

# User-specific files
*.suo
*.user
*.userosscache
*.sln.docstates

# Build results
[Dd]ebug/
[Dd]ebugPublic/
[Rr]elease/
[Rr]eleases/
x64/
x86/
build/
bld/
[Bb]in/
[Oo]bj/
msbuild.log
msbuild.err
msbuild.wrn

# Visual Studio 2015
.vs/
```

## Visual Studio Code and dotnet watch

The course suggest to add *dotnet watch* to the project dependencies. This step is not needed, with the new relase of netcore it is always available.

Start Visual Studio Code
```shell
code .
```

From the terminal pane of vsc run dotnet watch and test a small change on the value controller to see that everything is working
```shells
dotnet watch run
```

## Database configuration and initalization

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
    services.AddDbContext<DatingContext>(options => options.UseSqlite(Configuration.GetConnectionString("DatingDbConnection")));
}
...
```

Create an initial migration with EF

```shell
dotnet ef migrations add InitialMigration
dotnet ef database update
```

This command created a **Migrations** folder with 3 files.
The update applies the migration creating a new database.

## Database usage

Add a context to a new constructor in `ValuesController.cs`

```c#
...
private readonly DatingContext _context;

public ValuesController(DatingContext context)
{
    this._context = context;
}
...
```

Change the return type for the two get methods and add data loading from the db.

```c#
[HttpGet]
public ActionResult<IEnumerable<Value>> Get()
{
    var values = _context.Values.ToList();
    return Ok(values);
}

[HttpGet("{id}")]
public ActionResult<Value> Get(int id)
{
    var value = _context.Values.FirstOrDefault(x => x.Id == id);
    return Ok(value);
}
```
