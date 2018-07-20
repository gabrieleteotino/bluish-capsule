---
title: "Timezones"
date: 2018-07-20T10:20:31.296+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

View the dates with the correct timezone.

<!--more-->

## UtcNow and JSON

In the server side code we always use *UtcNow* and store all the dates in the UTC timezones.

The web server can be anywhere in the world and the operating system can use any kind of timezone or locale, when we use UTC we don't have to care about the timezone.

Angular date pipe use the browser local timezone by default. That is what we normally want for our applications: users want to read time in the same way they read their watches.

The problem is that *Newtonsoft* serializer default configuration sends the dates without any timezone information.

Change the serializer settings in mvc configuration in **Startup.ConfigureServices**

```csharp
services.AddMvc()
  .SetCompatibilityVersion(CompatibilityVersion.Version_2_1)
  .AddJsonOptions(options => {
      options.SerializerSettings.DateTimeZoneHandling = Newtonsoft.Json.DateTimeZoneHandling.Utc;
  });
```

Look at how the dates are sent in json before and after the change

```
Before
"created":"2018-07-19T13:01:30.687868"

After
"created":"2018-07-19T13:01:30.687868Z"
```

Note that there is the *Z* of the timezone and now Angular can correctly read the date.
