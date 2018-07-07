---
title: "User secrets"
date: 2018-07-07T17:37:03+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

<!--more-->

## Introduction

To use the cloud service we will store some private keys in the configuration files.

**appsettings.json** already contains a key for **TokenSecret** that should not be committed in a public repository.

.NET Core has [User Secrets](https://docs.microsoft.com/en-us/aspnet/core/security/app-secrets?view=aspnetcore-2.1&tabs=linux) which we can use to store application variables outside the application folder.

The file that stores the secrets ends up in one of the following locations:

- Windows: %APPDATA%\microsoft\UserSecrets\<userSecretsId>\secrets.json
- Linux: ~/.microsoft/usersecrets/<userSecretsId>/secrets.json
- Mac: ~/.microsoft/usersecrets/<userSecretsId>/secrets.json

## Setup a new key

Generate a new *GUID*  for the application (a nice extension is **Insert GUID** from *heaths.vscode-guid*)

Open **DatingApp.API.csproj** and add a key **UserSecretsId** with the new GUID.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <UserSecretsId>e6bed759-7533-4565-abce-739583090011</UserSecretsId>
</PropertyGroup>
```

Now we are going to generate a new **TokenSecret** for our authentication.

Remove or rename the **TokenSecret** key from **appsettings.json**. To avoid confusion to other users I choose to set the key to a descriptive value.

```json
...
"AppSettings": {
  "TokenSecret": "Set the key in secrets. View README for more info."
},
...
```

```shell
dotnet user-secrets set "AppSettings:TokenSecret" "super duper secret key"
```

Verify in the configuration file that the key was added.

```shell
cat ~/.microsoft/usersecrets/e6bed759-7533-4565-abce-739583090011/secrets.json
```

To remove a key

```shell
dotnet user-secrets remove "wrong:keyname"
```

## Usage

In netcore2.1 no additional configuration is necessary. Simply access the key like before.

```c#
Configuration.GetSection("AppSettings:TokenSecret").Value
```
