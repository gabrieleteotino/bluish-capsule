---
title: "Main photo updated"
date: 2018-07-10T20:59:24+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["webapi", "angular"]
categories: ["dev"]
draft: false
---

<!--more-->

Feature: when a new main photo is set in **photo-editor.component** we want the profile photo in **member-edit.component** to refresh with the new photo.

## Emit an event

Open **photo-editor.component.ts** and add a new *@Output* property that emit a new event. Then, in the *setMainPhoto* subscription, we will emit the event.

```typescript
...
@Output() mainPhotoChanged = new EventEmitter<string>();
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
        this.mainPhotoChanged.emit(photo.url);
      },
      error => {
        this.alertify.error(error);
      }
    );
}
...
```

## Consume an event

Open **member-edit.component.html** and attach the event to a new function **reloadMainPhoto**.

```html
...
<app-photo-editor [photos]="user.photos" (mainPhotoChanged)="reloadMainPhoto($event)"></app-photo-editor>
...
```

In **member-edit.component.ts** implement the new function.

```typescript
...
reloadMainPhoto(photoUrl) {
  this.user.profilePhotoUrl = photoUrl;
}
...
```
