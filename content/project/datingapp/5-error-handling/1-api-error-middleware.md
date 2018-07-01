---
title: "Api error middleware"
date: 2018-06-15T10:59:57.722+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore"]
categories: ["dev"]
draft: false
---

<!--more-->

## Exception handling

In **Startup.cs** method **Configure** there is a useful configuration for the development environment:

```csharp
...
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
}
else
{
    app.UseHsts();
}
...
```

When an unhandled exception is raised a custom page is generate with the stack trace and all the headers and cookies for that request. This page exposes a lot of information so the default is to show it only in development.

The **UseHsts** enables https strict control security, nothing to do with errors.

## Global exception handling

In the else case add a new middleware to the *OWIN* pipeline: an exception handler.

In this code we use an extension to the *Response* class **AddApplicationError**

```csharp
...
app.UseExceptionHandler(builder =>
{
    builder.Run(async context => {
        context.Response.StatusCode = (int)HttpStatusCode.InternalServerError;

        var exceptionFeature = context.Features.Get<IExceptionHandlerFeature>();
        if (exceptionFeature != null)
        {
            context.Response.AddApplicationError(exceptionFeature.Error.Message);
            await context.Response.WriteAsync(exceptionFeature.Error.Message);
        }
    });
});
...
```

## ApplicationError extension

Create a new folder **Helpers** and a new class **HttpResponseExtensions.cs**. Change the class to *static*.

Add the **AddApplicationError** used in **UseExceptionHandler**. We put the error information in the **Application-Error** header and we set the CORS for this header so that the browser don't block the header.

```csharp
public static class HttpResponseExtensions
{
    public static void AddApplicationError(this HttpResponse response, string message)
    {
        response.Headers.Add("Application-Error", message);
        response.Headers.Add("Access-Control-Expose-Headers", "Application-Error");
        response.Headers.Add("Access-Control-Allow-Origin", "*");
    }
}
```

Add the *using* in **Startup.cs**.

## Changing environment

In **AuthController** we have the same bug in **Login** and **Register**: the use of the **Username** property of an object that can be null.

Open Postman and create a new POST "Login null body", set the url and headers but leave the body completely empty.

Start the API in *Development* (it is the default, but just to be explicit)
```shell
dotnet run --environment=Development
```

Send the request, we see the page with the exception details.

Switch dotnet to *Production*
```shell
dotnet run --environment=Production
```

Send the request again: now the body contains only the error message and in the headers:
```json
Application-Error Object reference not set to an instance of an object.
```

## Fix the code

Lets fix the code.

- Add the *Required* attribute to both props in **UserForLogin.cs**
- Create a *Value Object* in **Models/Username.cs**
- Change the prop **Username** from *string* to *Username* in **UserForLogin.cs** and **UserForRegistration.cs**
- Remove the **ToLowerInvariant** from all the methods in **AuthController**

```csharp
public class Username : IEquatable<Username>, IEquatable<string>
{
    public string Value { get; }

    public Username(string username)
    {
        if (string.IsNullOrWhiteSpace(username)) throw new Exception("Username is invalid.");

        // Convert username to lowercase to avoid multiple user with similar names like "John" and "john"
        // Use invariant to avoid conflicts for users from different cultures
        this.Value = username.ToLowerInvariant();
    }

    public static implicit operator string(Username value) => value.Value;

    public static implicit operator Username(string value) => new Username(value);

    public override bool Equals(object obj)
    {
        var other = obj as Username;

        return other != null ? Equals(other) : Equals(obj as string);
    }

    public bool Equals(Username other)
    {
        return other != null && Value == other.Value;
    }

    public bool Equals(string other) => Value == other;

    public override int GetHashCode() => Value.GetHashCode();

    public override string ToString() => Value;

    public static bool operator ==(Username a, Username b)
    {
        if (ReferenceEquals(a, b)) return true;
        if (((object)a == null) || ((object)b == null)) return false;

        return a.Value == b.Value;
    }

    public static bool operator !=(Username a, Username b) => !(a == b);
}
```

A lot of code to replace a string but DDD is very useful in the long run. We will abstract some of this code in a generic class in the future.
