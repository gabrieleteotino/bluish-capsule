---
title: "Persisting changes"
date: 2018-06-30T17:07:16+02:00
subtitle: ""
author: Gabriele Teotino
tags: []
categories: []
draft: true
---

## DTO

Create a new DTO

```c#
public class UserForUpdate
{
    public string Introduction { get; set; }
    public string LookingFor { get; set; }
    public string Interests { get; set; }
    public string City { get; set; }
    public string Country { get; set; }
}
```

## Automapper

In **AutoMapperProfiles** add the mapping from the new DTO to *User*

```c#
...
CreateMap<UserForUpdate, User>();
...
```

## UpdateUser

Create a new *PUT* method **UpdateUser**. Remember that *PUT* methods must be idempotent.

```c#
...
// PUT: api/user/3
[HttpPut("{id}")]
public async Task<IActionResult> UpdateUser(int id, [FromBody] UserForUpdate userForUpdate)
{
    if (!ModelState.IsValid)
    {
        return BadRequest(ModelState);
    }

    // User is a ClaimsPrincipal defined in ControllerBase, we search for the first identity that has an id (NameIdentifier)
    var currentUserId = int.Parse(User.FindFirst(ClaimTypes.NameIdentifier).Value);

    var userFromRepo = await _repo.GetUser(id);

    if (userFromRepo == null)
    {
        return NotFound($"Could not find a user with an ID of {id}");
    }

    // Only the current user can change its profile
    if (userFromRepo.Id != currentUserId)
    {
        return Unauthorized();
    }

    _mapper.Map(userForUpdate, userFromRepo);

    if (await _repo.SaveAll())
    {
        // With PUT the response should be a 204 when everything is ok
        return NoContent();
    }
    else
    {
        throw new Exception($"Updating user {id} failed on save");
    }
}
...
```
