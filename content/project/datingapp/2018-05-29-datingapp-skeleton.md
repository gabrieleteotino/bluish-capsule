---
title: "Datingapp Skeleton"
date: 2018-05-29T16:49:07+02:00
subtitle: "A skeleton api for the dating app"
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore"]
categories: ["dev"]
draft: true
---

Create a folder
```
mkdir DatingApp
cd DatingApp
```

Create a new api project
```
dotnet new webapi -o DatingApp.API
cd DatingApp.API
```

Start the application
```
dotnet run
```

Check with the browser that the api is running navigating to (https://localhost:5001/api/values)[https://localhost:5001/api/values].

Create a basic data context and a test model.
