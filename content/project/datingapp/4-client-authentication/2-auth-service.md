---
title: "Auth service"
date: 2018-06-14T14:48:18+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular", "bootstrap", "jwt"]
categories: ["dev"]
draft: false
---

<!--more-->

## Create the Auth service

Inside **src/app** create a new folder **_services**. (The underscore is just a trick to keep the folder up).

Right click and select *Create Service*, name it **auth**.

Register the service in **app.module.ts** in the **providers** section.

```javascript
import { AuthService } from './_services/auth.service';
...
providers: [
  AuthService
],
```

Edit **auth.service.ts**

Add a prop **baseUrl** with the auth controller address and add a prop **userToken**.

Inject the **Http** service in the constructor. Check the import from '@angular/http'.

Implement the **login** method. Note that when the result is returned succesfully the JWT token is also stored in localStorage. This implementation is very rough but it will work for now.

```typescript
@Injectable()
export class AuthService {
  baseUrl = 'https://localhost:5001/api/auth/';
  userToken: any;

  constructor(private http: Http) {}

  login(model: any) {
    const header = new Headers({ 'Content-type': 'application/json' });
    const options = new RequestOptions({ headers: header });

    return this.http.post(this.baseUrl + 'login', model, options).pipe(
      map(response => {
        const user = response.json();
        if (user) {
          localStorage.setItem('token', user.tokenString);
          this.userToken = user.tokenString;
        }
      })
    );
  }
}
```

## Use the login service

Back in **nav.component.ts** inject the **AuthService** in the constructor and use it from the **login** function.

```typescript
...
login() {
  console.log(this.model);
  this.authService.login(this.model).subscribe(data => {
    console.log('Sucessfully logged in');
  }, error => {
    console.log('Login unsuccesful');
  });
}
...
```

## Hide the form for logged Users

Copy from the templates at [getbootstrap.com](http://getbootstrap.com/)

- switch to bootstrap 3.3.7
- navigate to *Components*
- click on the [Navbar](https://getbootstrap.com/docs/3.3/components/#navbar)
- inspect the code
- select all the code from the **ul** tag for the *dropdown*

In **nav.component.html** paste the code below the **loginForm**

Change the text to "Welcome user", remove a couple links and fix the text for the items in the dropdown. Also add icons.

The dropdown is temporarly not working, so we duplicate the logout functionality in the *Link* just before the dropdown.

Now we add the structural directive *ngIf* to the *ul* of the dropdown (snippet *a-ngIf*) and we bind it to a new function **loggedIn()**.

```html
<ul *ngIf="loggedIn()" class="nav navbar-nav navbar-right">
  <li><a (click)="logout()"><i class="fa fa-sign-out"></i> Logout</a></li>
  <li class="dropdown">
    <a href="#" class="dropdown-toggle" data-toggle="dropdown" role="button"
    aria-haspopup="true" aria-expanded="false">Welcome user <span class="caret"></span></a>
    <ul class="dropdown-menu">
      <li><a href="#"><i class="fa fa-user"></i> Edit profile</a></li>
      <li role="separator" class="divider"></li>
      <li><a (click)="logout()"><i class="fa fa-sign-out"></i> Logout</a></li>
    </ul>
  </li>
</ul>
```

Add a similar *ngIf* to **loginForm** binding it to **!loggedin()**

```html
<form *ngIf="!loggedIn()" #loginForm="ngForm" class="navbar-form navbar-right" (ngSubmit)="login()">
```

## loggedIn and logout

In **nav.component.ts** implement a rough version of **logout** by directly removing the *token*, and a **loggedIn** that checks if the *token* is present.

```typescript
logout() {
  this.authService.userToken = null;
  localStorage.removeItem('token');
}

loggedIn() {
  const token = localStorage.getItem('token');
  return !!token;
}
```

Now we can test the login functionality and see that if we succesfully log in the form disappears and the user combo shows up on the page. And when we log out the login form comes back.
