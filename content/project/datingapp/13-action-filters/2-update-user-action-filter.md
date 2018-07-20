---
title: "Update user action filter"
date: 2018-07-19T16:05:14.985+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

Create an action filter to apply to all the actions of **UserController** that updates the **LastActive** property of the current user.

<!--more-->

## Create the action filter

Create a class that implements [IAsyncActionFilter](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncactionfilter?view=aspnetcore-2.1).

The class is in a new folder under the controllers **Controllers/Filters/LogUserActivity.cs**. The position of the class is not relevant but I like to keep the filters near the controllers.

The generic **GetService** call requires *using Microsoft.Extensions.DependencyInjection;*, vscode auto import doesn't work.

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;
using DatingApp.API.Data;
using Microsoft.AspNetCore.Mvc.Filters;
using Microsoft.Extensions.DependencyInjection;

namespace DatingApp.API.Controllers.Filters
{
    public class LogUserActivity : IAsyncActionFilter
    {
        public async Task OnActionExecutionAsync(ActionExecutingContext context, ActionExecutionDelegate next)
        {
            var actionExecutedContext = await next();

            int userId = int.Parse(actionExecutedContext.HttpContext.User.FindFirst(ClaimTypes.NameIdentifier).Value);
            var repo = actionExecutedContext.HttpContext.RequestServices.GetService<IDatingRepository>();

            var currentUser = await repo.GetUser(userId);

            currentUser.LastActive = DateTime.UtcNow;

            await repo.SaveAll();
        }
    }
}
```

## Registration and usage

Register the filter in **Startup.ConfigureServices** as a *ServiceFilter* for *Dependency Injection*

```csharp
services.AddScoped<Controllers.Filters.LogUserActivity>();
```

Add the filter at the class level in **UserController** so that all the actions will call the filter.

```csharp
[ServiceFilter(typeof(Filters.LogUserActivity))]
[Authorize]
[Route("api/[controller]")]
public class UsersController : Controller
```
