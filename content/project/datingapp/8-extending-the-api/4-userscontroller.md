---
title: "UsersController"
date: 2018-06-19T14:22:53.453+02:00
subtitle: ""
author: Gabriele Teotino
tags: []
categories: []
draft: true
---

## Create the UsersController

Add a new class **UsersController** derived from *Controller*. Decorate it with *Authorize* and with a *Route*: "api/[controller]".

Add a constructor to inject the **DatingRepository**.

Add two methods to get all the users and a single user.

```cs
[Authorize]
[Route("api/[controller]")]
public class UsersController : Controller
{
    private readonly IDatingRepository _repo;
    public UsersController(IDatingRepository repo)
    {
        _repo = repo;
    }

    [HttpGet]
    public async Task<IActionResult> GetUsers()
    {
        var users = await _repo.GetUsers();

        return Ok(users);
    }

    [HttpGet("{id}")]
    public async Task<IActionResult> GetUser(int id)
    {
        var user = await _repo.GetUser(id);

        return Ok(user);
    }
}
```

## DTOs

Returning the internal model from an API call is plain wrong. So we need to map our **User** to a custom view model.

Create a new class **DTOs/PhotoForUser** with a subset of the Photo properties.
```cs
public class PhotoForUser
{
    public int Id { get; set; }
    public string Url { get; set; }
    public string Description { get; set; }
}
```

Create a new class **DTOs/UserForList** with a subset of the user properties. Note the *Username* value object and the *Age* instead of *DateOfBirth*. Other properties should be value objects: future refactoring.

```cs
public class UserForList
{
    public int Id { get; set; }
    public Username Username { get; set; }
    public string Gender { get; set; }
    public int Age { get; set; }
    public string KnownAs { get; set; }
    public DateTime Created { get; set; }
    public DateTime LastActive { get; set; }
    public string City { get; set; }
    public string Country { get; set; }
    public string ProfilePhotoUrl { get; set; }
}
```

Then another *user* class **DTOs/UserForDetail** with more information.

```cs
public class UserForDetail
{
    public int Id { get; set; }
    public Username Username { get; set; }
    public string Gender { get; set; }
    public int Age { get; set; }
    public string KnownAs { get; set; }
    public DateTime Created { get; set; }
    public DateTime LastActive { get; set; }
    public string Introduction { get; set; }
    public string LookingFor { get; set; }
    public string Interests { get; set; }
    public string City { get; set; }
    public string Country { get; set; }
    public PhotoForUser ProfilePhoto { get; set; }
    public ICollection<PhotoForUser> Photos { get; set; }
}
```

## Automapper

Using the nuget extension CTRL+SHIFT+p *NuGet Package Manager: Add Package* search **AutoMapper.Extensions.Microsoft.DependencyInjection** and add the latest version (now it is 4.0.1). Vscode will ask to restore packages, click *restore*.

In **Startup.ConfigureServices** add the service.

```cs
services.AddAutoMapper();
```

Now inject in the constructor of **UserController**.

```cs
public UsersController(IDatingRepository repo, IMapper mapper)
{
    _repo = repo;
    _mapper = mapper;
}
```

And then fix the two get methods to return view models.

```cs
[HttpGet]
public async Task<IActionResult> GetUsers()
{
    var users = await _repo.GetUsers();
    var usersVM = _mapper.Map<IEnumerable<UserForList>>(users);

    return Ok(usersVM);
}

[HttpGet("{id}")]
public async Task<IActionResult> GetUser(int id)
{
    var user = await _repo.GetUser(id);
    var userVM = _mapper.Map<UserForDetail>(user);

    return Ok(userVM);
}
```

## Automapper mapping configuration

Create a new class **Helpers/AutoMapperProfiles.cs** to store the mapping information.

For the age we first need an extension method for the date.

```cs
public static class DataTimeExtensions
{
    public static int CalculateAge(this DateTime birthDate)
    {
        var age = DateTime.Today.Year - birthDate.Year;
        // Correct if the birthday for this year is in the future
        if(birthDate.AddYears(age) > DateTime.Today) age--;

        return age;
    }
}
```

The other mapping are trivial.

```cs
public class AutoMapperProfiles : Profile
{
    public AutoMapperProfiles()
    {
        CreateMap<Photo, PhotoForUser>();

        CreateMap<User, UserForList>()
            .ForMember(dest => dest.Age, opt => opt.MapFrom(src => src.DateOfBirth.CalculateAge()))
            .ForMember(dest => dest.ProfilePhotoUrl, opt => opt.MapFrom(src => src.Photos.FirstOrDefault(x => x.IsMain).Url))
        ;

        CreateMap<User, UserForDetail>()
            .ForMember(dest => dest.Age, opt => opt.MapFrom(src => src.DateOfBirth.CalculateAge()))
            .ForMember(dest => dest.ProfilePhoto, opt => opt.MapFrom(src => src.Photos.FirstOrDefault(x => x.IsMain)))
            .ForMember(dest => dest.Photos, opt => opt.MapFrom(src => src.Photos.Where(x => !x.IsMain)))
        ;
    }
}
```

## Test

Test the user controller with Postman calling */api/users* and */api/users/1* (or any other valid id).

## Note

I removed the **Username** *value object*, it was not a good idea to use complex artifacts in a simple application. The lowercase conversions are moved inside the property setter of **User**

```cs
private string userName;
public string Username { get { return userName; } set { userName = value.ToLowerInvariant(); } }
```

And in **AuthRepository**

```cs
...
public async Task<User> Login(string username, string password)
{
    if(string.IsNullOrWhiteSpace(username)) return null;

    var user = await _context.Users.FirstOrDefaultAsync(x => x.Username == username.ToLowerInvariant());
...
public async Task<bool> UserExists(string username)
{
    if(string.IsNullOrWhiteSpace(username)) return false;

    return await _context.Users.AnyAsync(x => x.Username == username.ToLowerInvariant());
}
...
