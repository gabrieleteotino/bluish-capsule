---
title: "Extending the user model"
date: 2018-06-18T14:39:48.676+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

## New properties and classes

Open **User.cs** and add some new props. Collection properties must be initialized in the constructor.

```cs
public string Gender { get; set; }
public DateTime DateOfBirth { get; set; }
public string KnownAs { get; set; }
public DateTime Created { get; set; }
public DateTime LastActive { get; set; }
public string Introduction { get; set; }
public string LookingFor { get; set; }
public string Interests { get; set; }
public string City { get; set; }
public string Country { get; set; }

public ICollection<Photo> Photos { get; set; }

public User()
{
    Photos = new Collection<Photo>();
}
```

Create a class **Photo.cs** using CTRL+. on the squiggly line and choosing *Generate class 'Photo' in new file*.

Open **Photo.cs** and add some properties.

```cs
public class Photo
{
    public int Id { get; set; }
    public string Url { get; set; }
    public string Description { get; set; }
    public DateTime DateAdded { get; set; }
    public bool IsMain { get; set; }
}
```

Open **DatingContext.cs** and add a new *DbSet* for **Photo**.

## Migration

Stop any *dotnet run* or *dotnet watch run*

Add a new migration.

```shell
dotnet ef migrations add ExtendedUserClass
```

Look at the code for the migration and we see that the *onDelete* for the **Photos** table is not what we want.

Remove the last migration

```shell
dotnet ef migrations remove
dotnet ef migrations list
```

## Reverse migration

Just as an example let's see how to reverse a migration that was applied to the database

```shell
# Create a migration
dotnet ef migrations add ExtendedUserClass
# Apply to the DB
dotnet ef database update
# Revert to a previous migration, reverse the canges made to the database
dotnet ef database update AddedUsers
```

Some kind of operations are not supported by *Sqlite* so the update will fail. With other database (*SqlServer*, *MariaDB*, *Postgresql*) this operation just works.

If the database is just a development we can simply drop it and the recreate it. If this is not possible there are some workaround described in the page [SQLite EF Core Database Provider Limitations](https://docs.microsoft.com/en-us/ef/core/providers/sqlite/limitations).

```shell
dotnet ef database drop
dotnet ef migrations remove
dotnet ef database update
```

## Cascade delete relationship

Open **Phosot.cs** and reference back the **User** class. The **UserId** is not required to make the relationship work but is good to have in case we don't want to eagerly load.

```cs
...
public User User { get; set; }
public int UserId { get; set; }
...
```

Create a migration

```shell
dotnet ef migrations add ExtendedUserClass
```

Now if we look at the migration code we see that the **Photos** table has a not nullable **UserId** and a *cascade delete*.

```cs
...
UserId = table.Column<int>(nullable: false)
...
onDelete: ReferentialAction.Cascade);
```

Apply the migration
```shell
dotnet ef database update
```

Using *DB browser for SQLite* check the structure of the database.
