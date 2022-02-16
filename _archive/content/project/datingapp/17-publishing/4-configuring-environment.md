---
title: "Configuring environment"
date: 2018-08-07T15:12:55.018+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore"]
categories: ["dev"]
draft: false
---

<!--more-->

## Configure environment

[Use multiple environments in ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/environments?view=aspnetcore-2.1)

*Development* environment

```shell
export ASPNETCORE_ENVIRONMENT=Development
```

*Production* environment.

```shell
export ASPNETCORE_ENVIRONMENT=Production
```

Open **Properties/launchSettings.json** and change the **ASPNETCORE_ENVIRONMENT**

```json
// ...
"DatingApp.API": {
  "commandName": "Project",
  "launchBrowser": true,
  "launchUrl": "api/values",
  "applicationUrl": "https://localhost:5001;http://localhost:5000",
  "environmentVariables": {
    "ASPNETCORE_ENVIRONMENT": "Production"the application (insert your api keys and token secret in the
  }
}
// ...
```

Run the application (insert your api keys and token secret in the variables)

```shell
# Optional
export ASPNETCORE_ENVIRONMENT=Production

export AppSettings__TokenSecret=""
export CloudinarySettings__CloudName=""
export CloudinarySettings__ApiKey=""
export CloudinarySettings__ApiSecret=""
dotnet run
```
