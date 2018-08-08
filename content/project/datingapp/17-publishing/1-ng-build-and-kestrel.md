---
title: "NG Build and Kestrel"
date: 2018-08-07T11:16:12.603+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

Configure **angular.json** and select the **ouputhPath** to be in **../DatingApp.API/wwwroot**. The folder may not exist, create it.

Build the application

```shell
ng build
```

All the SPA files are now present in **../DatingApp.API/wwwroot**.


Configure the API to serve static files open **Startup.cs** and add to **Configure** the required configurations to allow the files to be retrieved from *wwwroot*

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, Seed seeder)
{
    // ...
    app.UseAuthentication();
    app.UseDefaultFiles();
    app.UseStaticFiles();
    app.UseMvc();
}
```

*UseDefaultFiles* allows for address without a filename to find **index.html**
*UseStaticFiles* serves the files from *wwwroot*

Create **Controllers/Fallback.cs** a Controller dedicated to handle all the paths that are not mapped by other routes.

```csharp
public class Fallback : Controller
{
    public IActionResult Index()
    {
        return PhysicalFile(Path.Combine(Directory.GetCurrentDirectory(), "wwwroot", "index.html"), "text/HTML");
    }
}
```

Add the new controller in **Startup.Configure**

```csharp
app.UseDefaultFiles();
app.UseStaticFiles();
app.UseMvc(routes =>
{
    routes.MapSpaFallbackRoute("spa-fallback", defaults: new { Controller = "Fallback", Action = "Index" });
});
```

With this configuration when a user open a url like https://siteurl/members the *fallback* intercepts it an sends to the SPA.
