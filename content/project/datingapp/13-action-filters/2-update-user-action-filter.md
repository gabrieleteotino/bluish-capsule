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

```csharp

```
