---
title: "Delete photos"
date: 2018-07-16T11:07:05.230+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

<!--more-->

## API delete

Open **PhotosController** and implement a new **DeletePhoto** method. This method will call the **destroy** method of [Cloudinary](https://cloudinary.com/documentation/image_upload_api_reference#destroy).

There is also an *if* to check for the photos created by our *seed* method because seeded photos are not uploaded to *Cloudinary*.

```csharp
[HttpDelete("{id}")]
public async Task<IActionResult> DeletePhoto(int userId, int id)
{
    // Only the current user can delete it's main photo
    var currentUserId = int.Parse(User.FindFirst(ClaimTypes.NameIdentifier).Value);
    if (userId != currentUserId)
    {
        return Unauthorized();
    }

    var photoToDelete = await _repo.GetPhoto(id);
    if (photoToDelete == null)
    {
        return NotFound($"The photo {id} does not exists.");
    }

    if (photoToDelete.IsMain)
    {
        return BadRequest("You cannot delete the main photo");
    }

    // The test photos are seeded without a PhotoId
    if (string.IsNullOrEmpty(photoToDelete.PublicId))
    {
        _repo.Delete(photoToDelete);
    }
    else
    {
        var result = _cloudinary.Destroy(new DeletionParams(photoToDelete.PublicId));
        if (result.Result == "ok")
        {
            _repo.Delete(photoToDelete);
        }
    }

    if (await _repo.SaveAll())
    {
        return Ok();
    }

    return BadRequest("Could not delete the photo");
}
```

## Test with Postman

With *Postman* authenticate then call *{{url}}/api/users/1* and search for the main photo id.

Create a new *DELETE* request **Delete Photo for User 1** with the url *{{url}}/api/users/1/photos/12* (change 12 to the main photo id).

Attach the debugger and check that this photo is not deletable: 400 "You cannot delete the main photo".

Choose another photo id that is not the main photo and send the request. This should return 200.

Try to delete the first photo of the user (the one with the lowest id), to check that also the case without **PublicId** is working.

## SPA delete

Open **user.service.ts** and add a new method **deletePhoto**.

```typescript
...
deletePhoto(userId: number, photoId:number) {
  return this.http
    .delete(this.baseUrl + userId + '/photos/' + photoId)
    .pipe(catchError(this.handleError));
}
...
```

Open **photo-editor.component.ts** and add a new method **deletePhoto**. In this function use alertify to ask for confirmation and *underscore* to manipualte the **photos** array (which in an *@Input*, so the changes will be reflected in the parent component).

```typescript
deletePhoto(photoId: number) {
  this.alertify.confirm('Are you sure you want to delete this photo?', () => {
    this.userService
      .deletePhoto(this.authService.getUserId(), photoId)
      .subscribe(
        () => {
          this.photos.splice(_.findIndex({ id: photoId }), 1);
          this.alertify.message('Photo has been deleted');
        },
        error => {
          console.log(error);
          this.alertify.error('Failed to delete photo');
        }
      );
  });
}
```

Open *photo-editor.component.html** and bind **deletePhoto** to the *click* event of the button

```html
<button class="btn btn-xs btn-danger" (click)="deletePhoto(photo.id)" [disabled]="photo.isMain">
  <i class="fa fa-trash-o"></i>
</button>
```
