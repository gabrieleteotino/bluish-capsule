---
title: "Sorting in the API"
date: 2018-07-25T12:38:59.447+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

<!--more-->

Add a property to **UserParams** to pass the ordering

```csharp
public string OrderBy { get; set; }
```

In **DatingRepository.GetUsers** add a default ordering by **LastActive** and add the custom ordering.

```csharp
public async Task<PagedList<User>> GetUsers(UserParams userParams)
{
    var users = _context.Users.Include(p => p.Photos).OrderByDescending(x => x.LastActive).AsQueryable();
    users = users.Where(x => x.Id != userParams.UserId && x.Gender == userParams.Gender);
    if (userParams.MinAge != UserParams.MinAgeDefault || userParams.MaxAge != UserParams.MaxAgeDefault)
    {
        // Precalculate the dates so the database can optimize the query
        var today = DateTime.Today;
        var minAgeDateOfBirth = today.AddYears(-userParams.MinAge);
        var maxAgeDateOfBirth = today.AddYears(-userParams.MaxAge - 1).AddDays(1);
        users = users.Where(x => x.DateOfBirth <= minAgeDateOfBirth && x.DateOfBirth >= maxAgeDateOfBirth);
    }
    if (!string.IsNullOrEmpty(userParams.OrderBy))
    {
        switch (userParams.OrderBy)
        {
            case "created":
                users = users.OrderByDescending(x=> x.Created);
                break;
            default:
                break;
        }
    }
    return await PagedList<User>.CreateAsync(users, userParams.PageNumber, userParams.PageSize);
}
```

## Test with Postman

Create a new request and add a **OrderBy** parameter

```
{{url}}/api/users?OrderBy=created
```
