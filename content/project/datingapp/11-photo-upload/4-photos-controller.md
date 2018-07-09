---
title: "Photos controller"
date: 2018-07-07T23:15:02+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

<!--more-->

## Photos controller

Create a new **PhotosController.cs** with *Authorization* and a custom route.

```c#
[Authorize]
[Route("api/users/{userId}/photos")]
public class PhotosController : Controller
{
}
```

Create a constructor for DI with the **IDatingRepository**, **IMapper** and the configuration for *Cloudinary*.
We also create a new **Account** and a new **Cloudinary** objects for *Cloudinary*.

```c#
private readonly IDatingRepository _repo;
private readonly IMapper _mapper;
private readonly IOptions<CloudinarySettings> _cloudinarySettings;
private Cloudinary _cloudinary;

public PhotosController(IDatingRepository repo, IMapper mapper, IOptions<CloudinarySettings> cloudinarySettings)
{
    _cloudinarySettings = cloudinarySettings;
    _mapper = mapper;
    _repo = repo;

    // Initialize Cloudinary
    Account account = new Account(
        cloudinarySettings.Value.CloudName,
        cloudinarySettings.Value.ApiKey,
        cloudinarySettings.Value.ApiSecret
    );

    _cloudinary = new Cloudinary(account);
}
```

To post a photo we need a new DTO **PhotoForCreation**

```c#
public class PhotoForCreation
{
    public string Url { get; set; }
    public IFormFile File { get; set; }
    public string  Description { get; set; }
    public DateTime Created { get; }
    public string PublicId { get; set; }

    public PhotoForCreation()
    {
        this.Created = DateTime.Now;
    }
}
```

Now we can create a new *POST* method **AddPhotoForUser**. Note that this method still needs a mapping configuration and have a TODO for the return value.

```c#
[HttpPost]
public async Task<IActionResult> AddPhotoForUser(int userId, PhotoForCreation photoDto)
{
    var user = await _repo.GetUser(userId);

    if (user == null)
    {
        return BadRequest("Could not find user");
    }

    var currentUserId = int.Parse(User.FindFirst(ClaimTypes.NameIdentifier).Value);

    // Only the current user can upload it's photos
    if (user.Id != currentUserId)
    {
        return Unauthorized();
    }

    var file = photoDto.File;

    if (file.Length > 0)
    {
        var uploadResult = new ImageUploadResult();

        using (var stream = file.OpenReadStream())
        {
            var uploadParams = new ImageUploadParams()
            {
                File = new FileDescription(file.Name, stream)
            };
            uploadResult = _cloudinary.Upload(uploadParams);
        }

        // Check if the upload was fine
        if(uploadResult.Error != null)
        {
            return BadRequest("Unable to upload photo.\n" + uploadResult.Error.Message);
        }

        // Save the results back in the DTO
        photoDto.Url = uploadResult.Uri.ToString();
        photoDto.PublicId = uploadResult.PublicId;

        // Map the dto to a Photo model
        var photo = _mapper.Map<Photo>(photoDto);
        photo.User = user;

        // Check if the photo has to be the main photo for the user
        if (!user.Photos.Any(x => x.IsMain))
        {
            photo.IsMain = true;
        }

        user.Photos.Add(photo);

        if (await _repo.SaveAll())
        {
            // TODO this Ok is just a temporary solution, we should return a CreatedAtRoute
            return Ok();
        }
    }
    return BadRequest("Could not add the photo");
}
```

To have a *CreatedAtRoute* we have to implement a *GET* method for the *photo*.

Open **IDatingRepository** and add a new signature.

```c#
Task<Photo> GetPhoto(int id);
```

And a concrete implementation in **DatingRepository**

```c#
public async Task<Photo> GetPhoto(int id)
{
    return await _context.Photos.FirstOrDefaultAsync(x => x.Id == id);
}
```

Create a new DTO **PhotoForReturn**

```c#
public class PhotoForReturn
{
    public int Id { get; set; }
    public string Url { get; set; }
    public string Description { get; set; }
    public DateTime DateAdded { get; set; }
    public bool IsMain { get; set; }
    public string PublicId { get; set; }
}
```

Create the mappings for both the DTOs in **AutoMapperProfiles**

```c#
...
CreateMap<PhotoForCreation, Photo>();
CreateMap<Photo, PhotoForReturn>();
...
```


Now we have all the pieces for the *GET* method in the controller

```c#
[HttpGet("{id}", Name="GetPhoto")]
public async Task<IActionResult> GetPhoto(int id) {
    var photoFromRepo = await _repo.GetPhoto(id);

    var photoForReturn = _mapper.Map<PhotoForReturn>(photoFromRepo);

    return Ok(photoForReturn);
}
```

Let's fix the *return* for **AddPhotoForUser**

```c#
...
if (await _repo.SaveAll())
{
    var photoForReturn = _mapper.Map<PhotoForReturn>(photo);
    return CreatedAtRoute("GetPhoto", new { id = photo.Id }, photoForReturn);
}
...
```
