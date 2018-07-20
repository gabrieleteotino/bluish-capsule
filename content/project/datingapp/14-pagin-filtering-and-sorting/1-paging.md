---
title: "Paging"
date: 2018-07-20T11:18:34.284+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

<!--more-->

## Pagin in the webapi

To paginate the data we will pass two paramaters via query string to the action.

https://localhost:5001/api/users?pageNumber=1&pageSize=5

The method **DatingRepository.GetUsers** returns an *IEnumerable* but in the code we actually return a *List* with *ToListAsync*. This method iterates on all the results in the *DbSet* and add them all to the list.

To substitute this method we will create a new class of type *List* that will filter the data and also tell the count of user and the count of pages.

Create a new class **PagedList** in the **Helpers** folder

```csharp

```
