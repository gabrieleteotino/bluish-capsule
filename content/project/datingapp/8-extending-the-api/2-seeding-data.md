---
title: "Extending the user model"
date: 2018-06-18T18:08:20+02:00
subtitle: ""
author: Gabriele Teotino
tags:["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

## Create the data

Generate some data using [json generator](https://next.json-generator.com).

This template will generate 5 female profiles

```
[
  '{{repeat(5)}}',
  {
    Gender: 'female',
    DateOfBirth: '{{date(new Date(1950,0,1), new Date(1999, 11, 31), "YYYY-MM-dd")}}',
    KnownAs: '{{firstName("female")}}',
    Username: function(){ return this.KnownAs.toLowerCase(); },
    Created: '{{date(new Date(2017,0,1), new Date(2017, 7, 31), "YYYY-MM-dd")}}',
    LastActive: function(){return this.DateCreated; },
    Introduction: '{{lorem(1, "paragraphs")}}',
    LookingFor: '{{lorem(1, "paragraphs")}}',
    Interests: '{{lorem(1, "sentences")}}',
    City: '{{city()}}',
    Country: '{{country()}}',
    Photos: [
        {
          url: function(num) {
          return 'https://randomuser.me/api/portraits/women/' + num.integer(1,99) + '.jpg';
        },
        isMain: true,
        description: '{{lorem()}}'
      }
    ]
  }
]
```

Change *female* to *male* and *women* to *men* to build 5 male profiles.

Copy the data generated in a file **Data/UserSeedData.json**, merge the two arrays in a single one.

## Refactoring

To seed the data we need to recalculate the *salt* and the *hash* like in **AuthRepository**. To avoid duplicated code we will refactor the methods to a new helper class.

Create a class **Helpers/HmacHelper.cs** move the two functions **ComputeHashSalt** and **CheckPasswordHash**, mark them as *public static*. Refactor **AuthRepository** to use the new class.

## Seed the data

Create a new class **Data/Seed.cs**, add a constructor and use a DI for **DatingContext**.

Create a method **SeedUsers**.

In **Startup.cs** add the **Seed** class as a *Transient* in **ConfigureServices**.

```cs
...
services.AddDbContext<DatingContext>(options => options.UseSqlite(Configuration.GetConnectionString("DatingDbConnection")));
services.AddTransient<Seed>();
services.AddCors();
...
```
Then change the signature of **Configure** to inject the **Seed**

```cs
...
public void Configure(IApplicationBuilder app, IHostingEnvironment env, Seed seeder)
...
seeder.SeedUsers();

app.UseHttpsRedirection();
...
```
