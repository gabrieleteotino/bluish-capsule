---
title: "Set main photo"
date: 2018-07-09T19:07:40+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular", "cloudinary", "postman", "underscore"]
categories: ["dev"]
draft: false
---

<!--more-->

# Add API method

Open **PhotosController** and create a new *POST* action **SetMainPhoto**.

```c#
[HttpPost("{id}/setMain")]
public async Task<IActionResult> SetMainPhoto(int userId, int id)
{
    // Only the current user can set it's main photo
    var currentUserId = int.Parse(User.FindFirst(ClaimTypes.NameIdentifier).Value);
    if (userId != currentUserId)
    {
        return Unauthorized();
    }

    var photoToSet = await _repo.GetPhoto(id);
    if (photoToSet == null)
    {
        return NotFound($"The photo {id} does not exists.");
    }

    if (photoToSet.IsMain)
    {
        return BadRequest("This is already the main photo");
    }

    var currentMainPhoto = await _repo.GetMainPhotoForUser(userId);
    if (currentMainPhoto != null)
    {
        currentMainPhoto.IsMain = false;
    }

    photoToSet.IsMain = true;

    if (await _repo.SaveAll())
    {
        return NoContent();
    }

    return BadRequest("Could not set the photo to main");
}
```

We also need to implement a simple **GetMainPhotoForUser** in the repository

```c#
public async Task<Photo> GetMainPhotoForUser(int userId)
{
    return await _context.Photos.FirstOrDefaultAsync(x => x.UserId == userId && x.IsMain);
}
```

## Test with postman

Login and update the token.

Call **{{url}}/api/users/1** and look in the response for the photos array. Only one has *IsMain: true*.

Create a new *POST* to **{{url}}/api/users/1/photos/11/setMain**, 11 is one of the other photos id from the previous request.

Call again **{{url}}/api/users/1** and check that now the photo with id 11 has *IsMain: true*.

Make a few other calls with incorrect ids to check the error messages.

## Call the new API method

Open **user.service.ts** and add a new method **setMainPhoto**. Note that we used a *POST* to keep thing simple and we pass an empty object as *body*.

```typescript
setMainPhoto(userId: number, photoId: number) {
  return this.http
    .post(this.baseUrl + userId + '/photos/' + photoId + '/setMain', {})
    .pipe(catchError(this.handleError));
}
```

Open **photo-editor.component.ts** and implement a new method **setMainPhoto** (add the necessary DI in the constructor).

```typescript
setMainPhoto(photo: Photo) {
  this.userService
    .setMainPhoto(this.authService.getUserId(), photo.id)
    .subscribe(
      () => {
        console.log('Main photo set');
      },
      error => {
        this.alertify.error(error);
      }
    );
}
```

Open **photo-editor.component.html** and change the **Main** button.

```html
<button class="btn btn-xs" [ngClass]="photo.isMain ? 'btn-default active' : 'btn-primary'" (click)="setMainPhoto(photo)"
        [disabled]="photo.isMain">Main</button>
```

Now the buttons change the main photos but still don't reflect the changes in the rest of the components.

## Underscore

Stop *ng serve*.

Add underscore and the typescript definition.

```shell
npm install underscore --save
npm install @types/underscore --save-dev
```

Start *ng serve*

Open **photo-editor.component.ts** and import *underscore* and change **setMainPhoto**.

```typescript
...
import * as _ from 'underscore';
...
setMainPhoto(photo: Photo) {
  this.userService
    .setMainPhoto(this.authService.getUserId(), photo.id)
    .subscribe(
      () => {
        const oldMain = _.findWhere(this.photos, { isMain: true });
        oldMain.isMain = false;
        photo.isMain = true;
        this.mainPhoto = photo;
      },
      error => {
        this.alertify.error(error);
      }
    );
}
```
