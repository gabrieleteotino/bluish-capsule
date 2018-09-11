---
title: "Like functionality in the API"
date: 2018-07-31T14:42:59.013+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "postman"]
categories: ["dev"]
draft: false
---

<!--more-->

## Repository

Open **IDatingRepository** and add new signature for **GetLike**.

```csharp
Task<Like> GetLike(int userId, int recipientId);
```

Open **DatingRepository** and implement **GetLike**.

```csharp
public async Task<Like> GetLike(int userId, int recipientId)
{
    return await _context.Likes
      .FirstOrDefaultAsync(x => x.LikerId == userId && x.LikeeId == recipientId);
}
```

## Controller

Open **UsersController** and add a new *POST* method **LikeUser**

```csharp
[HttpPost("{id}/like/{recipientId}")]
public async Task<IActionResult> LikeUser(int id, int recipientId) {
    var currentUserId = int.Parse(User.FindFirst(ClaimTypes.NameIdentifier).Value);
    if (id != currentUserId)
    {
        return Unauthorized();
    }

    var existingLike = await _repo.GetLike(id, recipientId);
    if (existingLike != null)
    {
        return BadRequest("You already like this user");
    }

    if(await _repo.GetUser(recipientId) == null) {
        return NotFound();
    }

    var like = new Like { LikerId = id, LikeeId = recipientId};

    _repo.Add<Like>(like);

    if (await _repo.SaveAll())
    {
        return Ok();
    }

    return BadRequest();
}
```

## Test in Postman

Create a new *POST* to this url

```html
{{url}}/api/users/1/like/12
```

To create a new Like from the user *1* to the user *12*.

The first time we send the request we will receive a *200* and if we send the request again we will receive a *400*.
