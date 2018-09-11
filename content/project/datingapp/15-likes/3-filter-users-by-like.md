---
title: "Filter user by Like"
date: 2018-08-01T09:20:27.589+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore"]
categories: ["dev"]
draft: false
---

<!--more-->

## Refactoring

I find the usage of the terms *Likers* and *Likees* a bit confusing.

The **Like** object is an arrow so the most natural names are *From* and *To*.

Downgrade the database and remove the last migration

```shell
dotnet ef migrations list
dotnet ef database update AddedPhotoId
dotnet ef migrations remove
```

Refactor using rename (F2)

```csharp
public class Like
{
    public int FromId { get; set; }
    public User From { get; set; }
    public int ToId { get; set; }
    public User To { get; set; }
}
```

Refactor also the **User** properties

```csharp
public ICollection<Like> LikesFrom { get; set; }
public ICollection<Like> LikeTo { get; set; }
```

And fix the code in **DatingContext** because the old naming confused me into a stupid error on the foreign key.
```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    // Define a primary key for Like
    modelBuilder.Entity<Like>()
        .HasKey(x => new { x.FromId, x.ToId });

    modelBuilder.Entity<Like>()
        .HasOne(l => l.From)
        .WithMany(u => u.LikeTo)
        .HasForeignKey(l => l.FromId)
        .OnDelete(DeleteBehavior.Restrict);

    modelBuilder.Entity<Like>()
        .HasOne(l => l.To)
        .WithMany(u => u.LikesFrom)
        .HasForeignKey(l => l.ToId)
        .OnDelete(DeleteBehavior.Restrict);
}
```

Recreate the migration

```shell
dotnet ef migrations add AddedLikeEntity
dotnet ef database update
```

## Expand UserParams

To get the list of liked or likers we add two new boolean property to **UserParams**

```csharp
public bool Likees { get; set; } = false;
public bool Likers { get; set; } = false;
```

Edit **DatingRepository.GetUsers** and add the new conditions

```csharp

// Users that likes the current user
if (userParams.Likers)
{
    // The users who have in the list of liked user a like pointing to the current user
    users = users.Where(x => x.LikeTo.Any(y => y.ToId == userParams.UserId));
}
// Users that the current user likes
if (userParams.Likees)
{
    // The users who have in the list of likers a like originated from the current user
    users = users.Where(x => x.LikesFrom.Any(y => y.FromId == userParams.UserId));
}
```

## Test with Postman

Create two request to the following urls

```html
{{url}}/api/users?likees=true
{{url}}/api/users?likers=true
```
