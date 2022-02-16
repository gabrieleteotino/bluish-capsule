---
title: "DatingRepository"
date: 2018-06-19T13:55:36.793+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore"]
categories: ["dev"]
draft: false
---

<!--more-->

## Interface

Create a new **IDatingRepository** with methods for **Add**, **Delete**, **SaveAll**, **GetUsers** and **GetUser**.

```cs
public interface IDatingRepository
{
     void Add<T>(T entity) where T: class;
     void Delete<T>(T entity) where T: class;
     Task<bool> SaveAll();
     Task<IEnumerable<User>> GetUsers();
     Task<User> GetUser(int id);
}
```

## Implementation

Create a new **DatingRepository** and implement **IDatingRepository**.

## Register

In **Startup.cs** **Configure** register the repository for DI.

```cs
services.AddScoped<IDatingRepository, DatingRepository>();
```
