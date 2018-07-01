---
title: "Angular JWT"
date: 2018-06-17T14:55:17+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

<!--more-->

The official [repository](https://github.com/auth0/angular2-jwt).

Note: here we are using version 2 of the library with Angular 6. The course used v1 with Angular5.
## Installation

Stop *ng serve* if it is running.

```shell
npm i --save @auth0/angular-jwt
```

Restart *ng serve*

## Component to Service refactoring

In **nav.component.ts** we are currently using **logout** and **loggedIn** wrong, they are changing internal behavior of the **auth.service.ts** and that is not good. The course code just fix the **loggedIn** function but this class implementation really annoys me, so I will fix both of them.

Open **auth.service.ts** and change the visibility of *userToken* and add constant for the token key in local storage.

```typescript
...
const LOCALSTORAGE_TOKEN_KEY = 'token';
...
private userToken: any;
...
```

Import the jwt service helper, assign it to a class property and create **loggedIn()** and **logout()** functions.

```typescript
...
import { JwtHelperService } from '@auth0/angular-jwt';
...
private readonly jwt = new JwtHelperService();
...
loggedIn() {
  const token: string = localStorage.getItem(LOCALSTORAGE_TOKEN_KEY);
  return token ? !this.jwt.isTokenExpired(token) : false;
}

logout() {
  this.userToken = null;
  localStorage.removeItem(LOCALSTORAGE_TOKEN_KEY);
}
...
```

And in **nav.component.ts** use the new functions.

```typescript
logout() {
  this.authService.logout();
  this.alertify.message('Logged out');
}

loggedIn() {
  const token = localStorage.getItem('token');
  return !!token;
}
```

Test login and logout features.

## Decode token

Here is another place where i didn't like the solution used by the course, so I took another route.

In **auth.service.ts** add a property to cache the decoded token.

```typescript
private decodedToken: any;
```

Then a function to load from local storage, this will be called when we are in a freshly loaded page.

```typescript
private loadTokenFromLocalStorage() {
  const token = localStorage.getItem(LOCALSTORAGE_TOKEN_KEY);
  if (token) {
    this.userToken = token;
    this.decodedToken = this.jwt.decodeToken(token);
  }
}
```

Using the new function we can safely load the *username* from the token

```typescript
getUsername() {
  if (!this.decodedToken) {
    this.loadTokenFromLocalStorage();
  }

  // the token can still be null because we are not logged in
  return this.decodedToken ? this.decodedToken.unique_name : '';
}
```

In **nav.component.ts**

```typescript
getUsername() {
  return this.authService.getUsername();
}
```

In **nav.component.html**

```html
<a href="#" class="dropdown-toggle" data-toggle="dropdown" role="button"
          aria-haspopup="true" aria-expanded="false">Welcome {{ getUsername() }} <span class="caret"></span></a>
```

Test the application, now the *username* is shown in the welcome menu.

Note: the course choose to capitalize the first letter of the username, I don't like that. A user can choose a nick name in lower case. This will be fixed when we will implement a profile page.
