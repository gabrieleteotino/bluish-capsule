---
title: "Controller"
date: 2018-06-12T11:26:48+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "postman"]
categories: ["dev"]
draft: false
---

<!--more-->

## DTO

Create a folder *DTOs*

In **DTOs** create a new class **UserForRegistration** with two props:

- string Username
- string Password

This object will be created as a target for the deserialization of the JSON sent to the **Register** action.

## Controller

In **Controllers** create a new class **AuthController** derived from *Controller*.

Add a constructor with a parameter for *DI*

```c#
private readonly IAuthRepository _repo;

public AuthController(IAuthRepository repo)
{
    _repo = repo;
}
```

Add a new action to handle a new user registration

```c#
[HttpPost("register")]
public async Task<IActionResult> Register([FromBody] UserForRegistration userForRegistration)
{
    // TODO validation

    // Convert username to lowercase to avoid multiple user with similar names like "John" and "john"
    // Use invariant to avoid conflicts for users from different cultures
    userForRegistration.Username = userForRegistration.Username.ToLowerInvariant();

    if(await _repo.UserExists(userForRegistration.Username)){
        return BadRequest("Username already in use");
    }

    var userToCreate = new Models.User{ Username = userForRegistration.Username };
    var createdUser = await _repo.Register(userToCreate, userForRegistration.Password);

    // TODO this 201 is just a temporary solution, we should return a path to the new entity
    return StatusCode(201);
}
```

## Testing with Postman
In Postman create a new folder **Auth** inside our colleection and add a new request.

Change the type to *POST* and set the url to *https://localhost:5001/api/auth/register*.

Switch to the *Body* tab, change to *raw* and select *JSON* from the drop down.

Add a payload to the body:

```json
{
	"Username": "John",
	"Password": "password"
}
```

In vscode activate the *debug* pane, from the top bar of the pane change to *.Net Core Attach*, then press the start button.
In the new popup select the *dotnet exec* for the DatingApp.API, be sure your do not select the dotnet watch process.

Add a breakpoint in the **Register** action.

Send the request from **Postman**. The request for the *Username* "John" will hit the breakpoint, step to see what is happening until **Postman** receives a *201 Created*.

If we send the same request a second time and we follow it using the debugger then the **UserExists** method finds a user already registered with that name and returns. **Postman** receives a *400 Bad Request* with a response body containing `Username already in use`.
