---
title: "EF relationships"
date: 2018-07-31T10:47:38.127+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "ef", "entity-framework"]
categories: ["dev"]
draft: false
---

<!--more-->

## Many to many relationship

EF core doesn't have a direct way to configure many to many.

We will create a new entity the **Like** and a **User** will have a one to many relationship using the *"send like"* property (**Likees**) and another using the *"receive like* property (**Likers**).

## The Like entity

Create a new class **Models/Like.cs**

```csharp
public class Like
{
    public int LikerId { get; set; }
    public User Liker { get; set; }
    public int LikeeId { get; set; }
    public User Likee { get; set; }
}
```

Add the new properties to the **User**

```csharp
public ICollection<Like> Likers { get; set; }
public ICollection<Like> Likees { get; set; }
```

Add a new *DbSet* for the **Like** table to **DatingContext** and define the relationship in an override of **OnModelCreating**.

```csharp
public DbSet<Like> Likes { get; set; }
```

Stop *dotnet run**

Add a new migration

```shell
dotnet ef migrations add AddedLikeEntity
```

Review the migration code. Note that ef created an index *IX_Likes_LikeeId* and not one for *LikerId*, that is because *LikerId* is the first part of the primary key so it is already indexed.

Apply the migration to the DB.

```shell
dotnet ef database update
```
