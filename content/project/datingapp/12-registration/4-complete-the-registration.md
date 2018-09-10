---
title: "Complete the registration"
date: 2018-07-19T13:48:27.228+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular"]
categories: ["dev"]
draft: false
---

In the SPA fix the methods used to post the registration.

<!--more-->

## Register

Open **register.component.ts** and remove the old **model: any** property and add a **user**.

```typescript
// model: any = {};
user: User;
```

Fix the **register** method.

```typescript
register() {
  console.log(JSON.stringify(this.registerForm.value));
  if (this.registerForm.valid) {
    this.user = Object.assign({}, this.registerForm.value);
    this.auth.register(this.user).subscribe(
      () => {
        this.alertify.success('registration complete');
      },
      error => {
        this.alertify.error(error);
      },
      () => {
        // After the registration login the user and redirect
        this.auth.login(this.user).subscribe(
          () => this.router.navigate(['/members'])
        );
      }
    );
  }
}
```

## Auth

Open **auth.service.ts** and refactor the **register** method to accept **User** objects.

```typescript
register(user: User) {
  return this.http
    .post(this.baseUrl + 'register', user, this.httpOptions())
    .pipe(catchError(this.handleError));
}
```

## Test

Test the application. When we register a new user everything is working. There is a little problem on the default profile photo, but the registration is fine.

## Fix the default photo

When a user login and he doesn't have a default photo we have to check the assignemnt to the *BehaviorSubject* in **auth.service.ts**.

Note that we cannot leave the default value because if multiple users login from the same browser then the last valid picture is shown. For example user 1 has a picture, logout, user 2 login then in the *BehaviorSubject* is still present the url of user 1.

```typescript
login(model: any) {
...
         if (this.user.profilePhotoUrl !== null) {
           this.mainPhotoUrl.next(this.user.profilePhotoUrl);
         }else {
           this.mainPhotoUrl.next(DEFAULT_USER_PICTURE);
         }
...
}

loadFromLocalStorage() {
...
    if (this.user.profilePhotoUrl !== null) {
      this.mainPhotoUrl.next(this.user.profilePhotoUrl);
    } else {
      this.mainPhotoUrl.next(DEFAULT_USER_PICTURE);
    }
...
}
```

Another problem is that when we upload a photo the server checks if it is the only photo and sets it to main. We have to check the response and set the photo.

Open **photo-editor.component.ts**

```typescript
this.uploader.onSuccessItem = (item, response, status, headers) => {
  if (response) {
    const photo: Photo = JSON.parse(response);
    this.photos.push(photo);
    // if this is the first photo uploaded by the user
    if (photo.isMain) {
      this.authService.changeMemberPhoto(photo.url);
    }
  }
};
```

The last problem is for other users in the **member-card.component.html** and in **member-detail.component.html**. Just add a ternary operator.

```html
<img src="{{user.profilePhotoUrl ? user.profilePhotoUrl : '../../../assets/user.png'}}" alt="{{user.knownAs}}">
```

```html
<img class="thumbnail profile-image" src="{{user.profilePhotoUrl ? user.profilePhotoUrl : '../../../assets/user.png'}}" alt="{{user.knownAs}}">
```

## Test

Test the application.

- When a new user registers in the navbar and in edit profile we see the default picture
- In the members gallery all the user that don't have a picture have the default
- When a user uploads the first photo the navbar and the edit profile photo are immediately updated
- Everything works after a refresh
