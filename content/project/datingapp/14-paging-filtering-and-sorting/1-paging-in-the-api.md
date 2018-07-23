---
title: "Paging in the API"
date: 2018-07-20T11:18:34.284+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

<!--more-->

## Support classes

To paginate the data we will pass two paramaters via query string to the action.

https://localhost:5001/api/users?pageNumber=1&pageSize=5

The method **DatingRepository.GetUsers** returns an *IEnumerable* but in the code we actually return a *List* with *ToListAsync*. This method iterates on all the results in the *DbSet* and add them all to the list.

To substitute this method we will create a new class of type *List* that will filter the data and also tell the count of user and the count of pages.

Create a new class **PagedList** in the **Helpers** folder

```csharp
public class PagedList<T> : List<T>
{
    public int CurrentPage { get; set; }
    public int PageSize { get; set; }
    public int TotalPages { get; set; }
    public int TotalCount { get; set; }

    public PagedList(List<T> items, int count, int pageNumber, int pageSize)
    {
        TotalCount = count;
        PageSize = pageSize;
        CurrentPage = pageNumber;
        TotalPages = (int)Math.Ceiling(count / (double)pageSize);

        this.AddRange(items);
    }

    public static async Task<PagedList<T>> CreateAsync(IQueryable<T> source, int pageNumber, int pageSize)
    {
        var count = await source.CountAsync();
        var items = await source.Skip((pageNumber - 1) * pageSize).Take(pageSize).ToListAsync();
        return new PagedList<T>(items, count, pageNumber, pageSize);
    }
}
```

The information about the pagination will be returned in a *header*, so create **Helpers/PaginationHeader.cs**

```csharp
public class PaginationHeader
{
    public int CurrentPage { get; set; }
    public int ItemsPerPage { get; set; }
    public int TotalItems { get; set; }
    public int TotalPages { get; set; }

    public PaginationHeader(int currentPage, int itemsPerPage, int totalItems, int totalPages)
    {
        CurrentPage = currentPage;
        ItemsPerPage = itemsPerPage;
        TotalItems = totalItems;
        TotalPages = totalPages;
    }
}
```

Add a new extension for the headers in **HttpResponseExtensions.cs**. Note that we instruct the serializer to use camel case (*currentPage* instead of *CurrentPage*)

```csharp
public static void AddPagination(this HttpResponse response, int currentPage, int itemsPerPage, int totalItems, int totalPages)
{
  var paginationHeader = new PaginationHeader(currentPage, itemsPerPage, totalItems, totalPages);
  var paginationHeaderSerialized = JsonConvert.SerializeObject(paginationHeader, new JsonSerializerSettings
  {
      ContractResolver = new CamelCasePropertyNamesContractResolver()
  });
  response.Headers.Add("Pagination", paginationHeaderSerialized);
  response.Headers.Add("Access-Control-Expose-Headers", "Pagination");
}
```

When the SPA calls **UsersController.GetUsers** it will now pass a parameter of type **UsersController.UserParams**

```csharp
public class UsersController : Controller
{
  // ...
  public class UserParams
  {
      private const int MaxPageSize = 50;
      public int PageNumber { get; set; } = 1;

      private int pageSize = 10;
      public int PageSize
      {
          get { return pageSize; }
          set { pageSize = (value > MaxPageSize) ? MaxPageSize : value; }
      }
  }
  // ...
}
```

## Repository and Controller

Now we can change the signature of **IDatingRepository**

```csharp
Task<PagedList<User>> GetUsers(Controllers.UsersController.UserParams paginationParams);
```

Update the implementation in **DatingRepository**

```csharp
public async Task<PagedList<User>> GetUsers(Controllers.UsersController.UserParams paginationParams)
{
    var users = _context.Users.Include(p => p.Photos);
    return await PagedList<User>.CreateAsync(users, paginationParams.PageNumber, paginationParams.PageSize);
}
```
var paginationHeader = new PaginationHeader(currentPage, itemsPerPage, totalItems, totalPages);
            var paginationHeaderSerialized = JsonConvert.SerializeObject(paginationHeader, new JsonSerializerSettings
            {
                ContractResolver = new CamelCasePropertyNamesContractResolver()
            });
            response.Headers.Add("Pagination", paginationHeaderSerialized);
            response.Headers.Add("Access-Control-Expose-Headers", "Pagination");
Update the implementation in **UsersController**

```csharp
[HttpGet]
public async Task<IActionResult> GetUsers([FromQuery] UserParams userParams)
{
    var users = await _repo.GetUsers(userParams);
    var usersVM = _mapper.Map<IEnumerable<UserForList>>(users);

    Response.AddPagination(users.CurrentPage, users.PageSize, users.TotalCount, users.TotalPages);
    return Ok(usersVM);
}
```

## Test with Postman

Create a new *GET* request in *Postman* with the following url

```
{{url}}/api/users?PageNumber=1&PageSize=3
```

In the response body we see only the first 3 users. In the *Headers* tab we find the **Pagination** header (here it is formatted for reading)

```json
{
  "currentPage": 1,
  "itemsPerPage": 3,
  "totalItems": 12,
  "totalPages": 4
}
```

## Refactor


REFACTOR https://stackoverflow.com/questions/43736000/pagination-in-a-net-core-api-project
