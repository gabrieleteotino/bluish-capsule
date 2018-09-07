---
title: "BehaviorSubject any to any communication"
date: 2018-07-16T09:41:50.646+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular"]
categories: ["dev"]
draft: false
---

Use a [BehaviorSubject](http://reactivex.io/rxjs/manual/overview.html#behaviorsubject) to enable any to any component comunication.

<!--more-->

## Motivation and implementation

Enabling the main photo refresh in the navbar is not possible using the classic *@Output* and *EventEmitter* because the two components are too far away in the hierarchy.

A *BehaviorSubject* is both an *Observer* and an *Observable* and it only remembers the last publication.

In this case we will add a *BehaviorSubject* to **auth.service.ts** and in **nav.component.ts** we will subscribe to it.
Meanwhile in **photo-editor.component.ts** we will remove the **mainPhotoChanged** *EventEmitter* and call the **auth.service.ts** to update the photo.

## auth.service

Open **auth.service.ts** add a new property for the *BehaviorSubject* and a public *Observable*.

Add a default photo image in the assets folder.

```typescript
private mainPhotoUrl = new BehaviorSubject<string>('../../assets/user.png');
currentPhotoUrl = this.mainPhotoUrl.asObservable();
```

Remove the **getUser** function.

Add a new function to be called by **photo-editor**

```typescript
changeMemberPhoto(photoUrl: string) {
  this.mainPhotoUrl.next(photoUrl);
  this.user.profilePhotoUrl = photoUrl;
  localStorage.setItem(LOCALSTORAGE_USER_KEY, JSON.stringify(this.user));
}
```

Refactor **login** and **loadFromLocalStorage**

```typescript
...
login(model: any) {
  return this.http
    .post(this.baseUrl + 'login', model, this.httpOptions())
    .pipe(
      map((response: any) => {
        if (response) {
          const token = response.tokenString;
          localStorage.setItem(LOCALSTORAGE_TOKEN_KEY, token);
          this.userToken = token;
          this.decodedToken = this.jwt.decodeToken(token);

          this.user = response.user;
          localStorage.setItem(LOCALSTORAGE_USER_KEY, JSON.stringify(this.user));
          this.mainPhotoUrl.next(this.user.profilePhotoUrl);
        }
      }),
      catchError(this.handleError)
    );
}
...
loadFromLocalStorage() {
  const token = localStorage.getItem(LOCALSTORAGE_TOKEN_KEY);
  if (token) {
    this.userToken = token;
    this.decodedToken = this.jwt.decodeToken(token);
  }
  const user = localStorage.getItem(LOCALSTORAGE_USER_KEY);
  if (user) {
    this.user = JSON.parse(user);
    this.mainPhotoUrl.next(this.user.profilePhotoUrl);
  }
}
...
```

## nav.component

Open **nav.component.ts** and add a new property to store the url and subscribe to the **BehaviorSubject** *Observer*. Remove the method **getPhotoUrl**.

```typescript
...
photoUrl: string;
...
ngOnInit() {
  this.authService.currentPhotoUrl.subscribe(next => this.photoUrl = next);
}
...
```

Open **nav.component.html** and update the markup

```html
<img src="{{photoUrl}}" alt="{{getUsername()}}">
```

## member-edit.component

Open **member-edit.component.ts** and remove the method **reloadMainPhoto** and the binding to **mainPhotoChanged** in html.

```html
<app-photo-editor [photos]="user.photos"></app-photo-editor>
```

Subscribe to the **BehaviorSubject** *Observer*.

```typescript
ngOnInit() {
  this.route.data.subscribe(data => {
    this.user = data['user'];
  });

  this.authService.currentPhotoUrl.subscribe(next => { this.user.profilePhotoUrl = next; });
}
```

## photo-editor.component

Open **photo-editor.component.ts** and remove the *@Output* property **mainPhotoChanged**.

Then in the function **setMainPhoto** instead of emitting the event we call the **authService** method **changeMemberPhoto**

```typescript
setMainPhoto(photo: Photo) {
  this.userService
    .setMainPhoto(this.authService.getUserId(), photo.id)
    .subscribe(
      () => {
        const oldMain = _.findWhere(this.photos, { isMain: true });
        oldMain.isMain = false;
        photo.isMain = true;
        this.mainPhoto = photo;
        this.authService.changeMemberPhoto(photo.url);
      },
      error => {
        this.alertify.error(error);
      }
    );
}
```

Test the application:

- Setting a main photo will immediately change the profile photo in the profile editor and in the navbar.
- Refreshing the page will load the last saved photo from *localStorage*.
