---
title: "Photo in the navbar"
date: 2018-07-12T18:07:43+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: false
---

Feature: load the main photo in the dropdown of the **nav.component**.

<!--more-->

## Return the user after login

Open **AuthController.cs** and add **IMapper** to the constructor then return a user view model to response.

```csharp
...
var userForList = _mapper.Map<UserForList>(user);

return Ok(new { tokenString, user = userForList });
...
```

## Store the user in localstorage

Open **auth.service.ts** and refactor to load the user.

Add a property **user** and a method **getUser**.

Change **login** and **logout** to save the user in local storage.

Rename **loadTokenFromLocalStorage** to **loadFromLocalStorage** and remove the old calls (my mistake, it was unnecessary).

```typescript
...
const LOCALSTORAGE_USER_KEY = 'user';
...
private user: User;
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

          const user = response.user;
          localStorage.setItem(LOCALSTORAGE_USER_KEY, JSON.stringify(user));
          this.user = user;
        }
      }),
      catchError(this.handleError)
    );
}

logout() {
  this.userToken = null;
  this.decodedToken = null;
  localStorage.removeItem(LOCALSTORAGE_TOKEN_KEY);
  localStorage.removeItem(LOCALSTORAGE_USER_KEY);
}

getUsername() {
  // the token can still be null because we are not logged in
  return this.decodedToken ? this.decodedToken.unique_name : '';
}

getUserId() {
  // the token can still be null because we are not logged in
  return this.decodedToken ? this.decodedToken.nameid : '';
}

getToken() {
  return this.userToken;
}

getUser(): User {
  return this.user;
}

private loadFromLocalStorage() {
  const token = localStorage.getItem(LOCALSTORAGE_TOKEN_KEY);
  if (token) {
    this.userToken = token;
    this.decodedToken = this.jwt.decodeToken(token);
  }
  const user = localStorage.getItem(LOCALSTORAGE_USER_KEY);
  if (user) {
    this.user = JSON.parse(user);
  }
}

```

Open **app.component.ts** and call **loadFromLocalStorage** during *Init*

```typescript
export class AppComponent implements OnInit {
  title = 'app';

  constructor(private authService: AuthService) {}

  ngOnInit(): void {
    this.authService.loadFromLocalStorage();
  }
}
```

## Add the image

Open **nav.component** and add a property for **user** and load it from **authService**.

```typescript
...
getPhotoUrl() {
  return this.authService.getUser().profilePhotoUrl;
}
...
```

```html
...
<ul *ngIf="loggedIn()" class="nav navbar-nav navbar-right">
  <li>
    <img src="{{getPhotoUrl()}}" alt="{{getUsername()}}">
  </li>
  <li class="dropdown" dropdown>
...
```

```css
...
img {
    border: 2px solid white;
    max-height: 50px;
}
```

## Fix the webapi

Now the application is broken. To restore it in the browser development tools delete the *token* from *localstorage*. Reload and login.

The app is now working but the photo is still missing.

This because the mapper in **AuthController** searches in the **Photos** and the **Login** method in **AuthRepositry** doesn't include them.

Open **AuthRepositry** and change the ef query.

```csharp
...
var user = await _context.Users
    .Include(x => x.Photos)
    .FirstOrDefaultAsync(x => x.Username == username.ToLowerInvariant());
...
```

Test the application and now the photo is loaded.
