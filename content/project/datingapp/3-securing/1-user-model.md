---
title: "User Model"
date: 2018-06-08T14:02:03+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

## Create the model

In the **Models** folder create anew **User** class.

Create properties for *Id*, *Username*, *PasswordHash* and *Passwordsalt*.

## Add the model to the Context
Open **DatingContext** and add a  new *DbSet* of type **User**

## Scaffold a migration

If the application is running stop it.

In the console add a new migration

```shell
dotnet ef migrations add AddedUserModel
```

In the **Migrations** folder check the new migration. Look for tables, fields and types to see that everything is like we expected.

Now run the migration against the database

```shell
dotnet ef database update
```
