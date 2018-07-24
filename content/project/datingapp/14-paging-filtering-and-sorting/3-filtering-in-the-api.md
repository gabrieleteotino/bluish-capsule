---
title: "Filtering in the API"
date: 2018-07-24T12:27:05.719+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

<!--more-->

## Show only users of the opposite gender

Edit **UserParams** and add two new properties for the filtering

```csharp
public int UserId { get; set; }
public string Gender { get; set; }
```

In **UsersController.GetUsers** initalize the *UserId* from the authentication (eventually overwriting whatever was passed in) and add a default *Gender* if nothing was specified.

```csharp
[HttpGet]
public async Task<IActionResult> GetUsers([FromQuery] UserParams userParams)
{
    var currentUserId = int.Parse(User.FindFirst(ClaimTypes.NameIdentifier).Value);

    var userFromRepo = await _repo.GetUser(currentUserId);
    if (userFromRepo == null)
    {
        return NotFound($"Could not find a user with an ID of {currentUserId}");
    }

    userParams.UserId = currentUserId;
    if (string.IsNullOrEmpty(userParams.Gender))
    {
        userParams.Gender = userFromRepo.Gender == "male" ? "female" : "male";
    }

    var users = await _repo.GetUsers(userParams);
    var usersVM = _mapper.Map<IEnumerable<UserForList>>(users);

    Response.AddPagination(users.CurrentPage, users.PageSize, users.TotalCount, users.TotalPages);
    return Ok(usersVM);
}
```

In **DatingRepository.GetUsers** add the filter condition. Note the casting to *AsQueryable* that enables to add where clauses.

```csharp
public async Task<PagedList<User>> GetUsers(Controllers.UsersController.UserParams userParams)
{
    var users = _context.Users.Include(p => p.Photos).AsQueryable();
    users = users.Where(x=>x.Id != userParams.UserId && x.Gender == userParams.Gender);
    return await PagedList<User>.CreateAsync(users, userParams.PageNumber, userParams.PageSize);
}
```

## Test with postman

If we send again the request to

```
{{url}}/api/users?PageNumber=1&PageSize=3
```

We now obtain only male users (if we are logged in as a female).

Also we can force a specific gender

```
{{url}}/api/users?PageNumber=1&PageSize=3&Gender=female
```

We obtain a list of only female users without the current logged in user

It is possible to mix and match all the params (PageNumber, PageSize and Gender) because we have implemented valid defaults.

## Filtering by age

Add two new properties to **UserParams** with default values.

```csharp
public static readonly int MinAgeDefault = 18;
public static readonly int MaxAgeDefault = 99;
public int MinAge { get; set; } = MinAgeDefault;
public int MaxAge { get; set; } = MaxAgeDefault;
```

In **DatingRepository.GetUsers** add the filter condition only if one of the two properties was changed, this will reduce the query workload and all our users are between 18 and 99.


```csharp
public async Task<PagedList<User>> GetUsers(UserParams userParams)
{
    var users = _context.Users.Include(p => p.Photos).AsQueryable();
    users = users.Where(x=>x.Id != userParams.UserId && x.Gender == userParams.Gender);
    if (userParams.MinAge != UserParams.MinAgeDefault || userParams.MaxAge != UserParams.MaxAgeDefault)
    {
        // To optimize the query we must precalculate the dates
        var today = DateTime.Today;
        var minAgeDateOfBirth = today.AddYears(-userParams.MinAge);
        var maxAgeDateOfBirth = today.AddYears(-userParams.MaxAge - 1).AddDays(1);
        users = users.Where(x => x.DateOfBirth <= minAgeDateOfBirth && x.DateOfBirth >= maxAgeDateOfBirth);
    }
    return await PagedList<User>.CreateAsync(users, userParams.PageNumber, userParams.PageSize);
}
```

Note that this query will not be optimized if we use the extension method **CalculateAge** because it can't run on the database server. If we use the simple but wrong version all the users will be loaded from the database and the filter applied later. The result will be the same but the performance will degrade a lot.

**WRONG**

```csharp
users = users.Where(x=>x.DateOfBirth.CalculateAge() >= userParams.MinAge && x.DateOfBirth.CalculateAge() <= userParams.MaxAge);
```

## Test with Postman

Create a new request and filter it by age

```
{{url}}/api/users?MinAge=20&MaxAge=30
```
