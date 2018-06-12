---
title: "6 Authentication Middleware"
date: 2018-06-12T17:41:05+02:00
subtitle: ""
author: Gabriele Teotino
tags: []
categories: []
draft: true
---

## Store the key

To generate and consume tokens wew need a secret key. We will store the key in the configuration file. This is just to keep things going but we should not leave the key in the configuration file because this file will be stored on github.

Open **appsetting.json** and add a new **AppSettings** section with a property **TokenSecret** containing the secret key.
```json
"AppSettings": {
  "TokenSecret": "super duper secret key"
},
```

## Modify the Auth controller

In the **AuthController** constructor add a DI for IConfiguration and add a property.
Then substitute the hard coded key JwtSecurityTokenHandler

```c#
var key = System.Text.Encoding.UTF8.GetBytes(_config.GetSection("AppSettings:TokenSecret").Value);
```

## Configure Middleware

In **Startup.cs** add the required configuration in **ConfigureServices**

```c#
var key = System.Text.Encoding.UTF8.GetBytes(Configuration.GetSection("AppSettings:TokenSecret").Value);
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
  .AddJwtBearer(options => {
    options.TokenValidationParameters = new TokenValidationParameters {
      ValidateIssuerSigningKey = true,
      IssuerSigningKey = new SymmetricSecurityKey(key),
      ValidateIssuer = false,
      ValidateAudience = false
    };
  });
```

In **Configure**, just before **UseMvc** add
```c#
app.UseAuthentication();
```

## Configure Controllers

The **AuthController** will be reachable without any authentication.
Se we can use the **ValuesController** to experiment.

Add a class decorator *Authorize* and all the actions will require a token.

```c#
...
[Route("api/[controller]")]
[ApiController]
[Authorize]
public class ValuesController : ControllerBase
{
  ...
```

If we want we can allow access to single actions by adding the decorator *AllowAnonymous*

## Test with Postman

Now if we call *api/values* we get a 400 Unhauthorized.

We have to post a username and password to login, copy and paste the token from the response.
Edit the headers for the GET *api/values* adding a **Authorization** key and as the value set "Bearer " followed by the token.

For example: `Bearer eyJ0eXAiOiJKV1QiLA0KICJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJqb2UiLA0KICJleHAiOjEzMDA4MTkzODAsDQogImh0dHA6Ly9leGFtcGxlLmNvbS9pc19yb290Ijp0cnVlfQ.dBjftJeZ4CVP-mB92K27uhbUJU1p1r_wW1gFWFOEjXk`

If we send the new request we now get 200 Ok with the response.
