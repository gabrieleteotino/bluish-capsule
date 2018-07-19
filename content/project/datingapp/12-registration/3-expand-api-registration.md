---
title: "Expand the api registration"
date: 2018-07-19T09:03:14.091+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

Adapt the webapi to the new registration form.

<!--more-->

## DTO

Open **UserForRegistration.cs** and add the new fields and the relative *Required* attributes. We ignore the **confirmPassword** field, it is only for the client validation.

Add two readonly porperties **Created** and **LastActive** initialized to now in the constructor.

```csharp
public class UserForRegistration
{
    [Required]
    public string Username { get; set; }

    [Required]
    [StringLength(30, MinimumLength = 8, ErrorMessage = "The password must be between 8 and 30 characters.")]
    public string Password { get; set; }

    [Required]
    public string Gender { get; set; }

    [Required]
    public string KnownAs { get; set; }

    [Required]
    public DateTime DateOfBirth { get; set; }

    [Required]
    public string City { get; set; }

    [Required]
    public string Country { get; set; }

    public DateTime Created { get; }

    public DateTime LastActive { get; }

    public UserForRegistration()
    {
        this.Created = DateTime.UtcNow;
        this.LastActive = DateTime.UtcNow;
    }
}
```

Open **AutoMapperProfiles.cs** and add a new profile. All the property names match, so no additional configuration is needed.

```csharp
CreateMap<UserForRegistration, User>();
```

## Controller

Open **AuthController.cs** and add a mapping.

```csharp
//var userToCreate = new Models.User { Username = userForRegistration.Username };
var userToCreate = _mapper.Map<User>(userForRegistration);
var createdUser = await _repo.Register(userToCreate, userForRegistration.Password);
```

To remove the *201* and substitute it with a *CreatedAtRoute* open **UsersController.cs** and add a name to the route of **GetUser**

```java
[HttpGet("{id}", Name="GetUser")]
public async Task<IActionResult> GetUser(int id)
```

Back in **AuthController.cs** change the *return*

```csharp
//return StatusCode(201);
var userToReturn = _mapper.Map<UserForDetail>(createdUser);
return CreatedAtRoute("GetUser", new { id = userToReturn.Id }, userToReturn);
```

## Test with Postman

Open Postman and edit the old *POST* used to create a user (or add a new one).

Change the body to the new structure. To avoid typing the JSON we can use the SPA, because in the register method we log a *JSON.stringify* of the form. For example:

```JSON
{
   "gender":"male",
   "username":"Mario",
   "knownAs":"Mariolino",
   "dateOfBirth":"2000-02-01T23:00:00.000Z",
   "city":"Rome",
   "country":"Italy",
   "password":"password",
   "confirmPassword":"password"
}
```

Add a breakpoint in **AuthController.Register()** then attach the debugger.

Send the request. We must receive a *201* with the JSON for the new user

```JSON
{
    "id": 12,
    "username": "mario",
    "gender": "male",
    "age": 18,
    "knownAs": "Mariolino",
    "created": "2018-07-19T09:41:10.1985833Z",
    "lastActive": "2018-07-19T09:41:10.1985834Z",
    "introduction": null,
    "lookingFor": null,
    "interests": null,
    "city": "Rome",
    "country": "Italy",
    "profilePhotoUrl": null,
    "photos": []
}
```

And in the headers there is the location for the new user, in my case: https://localhost:5001/api/Users/12
