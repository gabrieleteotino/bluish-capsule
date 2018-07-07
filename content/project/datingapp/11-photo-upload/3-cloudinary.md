---
title: "Where to store photos"
date: 2018-07-07T17:37:03+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

<!--more-->

## Registration and secrets

Register a new free account at [Cloudinary](https://cloudinary.com/invites/lpov9zyyucivvxsnalc5/sqiocmogqhvi7zmjbmjs).

Go into the *Dashboard* and look for
- Cloud name
- API Key
- API Secret

Open **appsettings.json** and add the template for the keys

```json
"CloudinarySettings" : {
  "CloudName": "Set the key in secrets. View README for more info.",
  "ApiKey": "Set the key in secrets. View README for more info.",
  "ApiSecret": "Set the key in secrets. View README for more info."
}
```

Using the console create the three key in secret storage.

```shell
dotnet user-secrets set "CloudinarySettings:CloudName" "******"
dotnet user-secrets set "CloudinarySettings:ApiKey" "******"
dotnet user-secrets set "CloudinarySettings:ApiSecret" "******"
```

If the **ApiSecret** starts with *-* or *--* there is a [bug](https://github.com/aspnet/DotNetTools/issues/309) in the command line that will result in a message:

```
Unrecognized option 'your ApiSecret value'
```

Workaround: create a file **cloudinarysettings.json** containing your settings.

```json
{
  "CloudinarySettings": {
    "CloudName": "******",
    "ApiKey": "******",
    "ApiSecret": "******"
  }
}
```

Pipe the configuration file in secret storage

```shell
cat ./cloudinarysettings.json | dotnet user-secrets set
```

Remember to put this file in gitignore or in a directory not in source control.

## Helper class

Create a new class **Helpers/CloudinarySettings.cs** with properties for the Cloudinary keys.

```c#
public class CloudinarySettings
{
    public string CloudName { get; set; }
    public string ApiKey { get; set; }
    public string ApiSecret { get; set; }
}
```

Open **Startup.cs** and add to **ConfigureServices** the configuration for the settings helper so that it is available for injection.

```c#
...
services.AddCors();
services.Configure<CloudinarySettings>(Configuration.GetSection("CloudinarySettings"));
services.AddAutoMapper();
...
```

## Change the Photo class

When we upload a photo to Cloudinary the [documentation](https://cloudinary.com/documentation/dotnet_image_upload#server_side_upload) says that the call returns a JSON object. Inside this object we have a key **public_id**. We need to store this key inside our photo object.

Open **Photo.cs** and add a prop for **public_id**.

```c#
public class Photo
{
    ...
    public string PublicId { get; set; }
}
```

Add a new migration.

```shell
dotnet ef migrations add AddedPhotoId
```

Check the migration code and if everithing is ok apply it to the DB.

```shell
dotnet ef database update
```

## Cloudinary package

CTRL+SHIFT+p Nuget Add Package

Search *CloudinaryDotNet* and add the latest version. (At the time of writing it was 1.3.1)

Restore to install the new package

```shell
dotnet restore
```

Now run the application to check that it builds correctly.
