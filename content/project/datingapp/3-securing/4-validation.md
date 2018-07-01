---
title: "Validation"
date: 2018-06-12T14:37:11+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore"]
categories: ["dev"]
draft: false
---

<!--more-->

## Data validation

To validate data we use DataAnnotation on the DTO classes.

Open **UserForRegistration.cs** and add a property decorator **Required** to both the properties. This will ensure that both properties are not null. Also add a minimum length for the password.

```c#
[Required]
[StringLength(30, MinimumLength = 8, ErrorMessage = "The password must be between 8 and 30 characters.")]
public string Password { get; set; }
```

Check in the controller action **Register** using the model validation.

```c#
if(!ModelState.IsValid) {
    return BadRequest(ModelState);
}
```

The code that check if the Username is already taken is a *Business Validation*, so we move that code above the check *ModelState.IsValid* and if there is an error we add the error to the model state. Doing this we obtain a consistent response in case of errors so that client applications can use a single method to display validation errors.

```c#
public async Task<IActionResult> Register([FromBody] UserForRegistration userForRegistration)
{
    // Convert username to lowercase to avoid multiple user with similar names like "John" and "john"
    // Use invariant to avoid conflicts for users from different cultures
    userForRegistration.Username = userForRegistration.Username.ToLowerInvariant();

    // Business Validation
    if (await _repo.UserExists(userForRegistration.Username))
    {
        ModelState.AddModelError("Username", "Username already in use.");
    }

    if (!ModelState.IsValid)
    {
        return BadRequest(ModelState);
    }

    var userToCreate = new Models.User { Username = userForRegistration.Username };
    var createdUser = await _repo.Register(userToCreate, userForRegistration.Password);

    // TODO this 201 is just a temporary solution, we should return a path to the new entity
    return StatusCode(201);
}
```

***

A personal note on what constitutes *Business Validation*: strictly speaking testing that the password length is in the required leght **is** a business rule, but I like to differentiate between simple *Model Validation* where we check if the objects have valid data and more complex validation rules. For example a bank transaction can have a valid amount of 100 (it is an integer greater than zero) but we also do a *Business Validation* to check if the customer have at least 100 in his account.
