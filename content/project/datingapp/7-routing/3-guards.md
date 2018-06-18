---
title: "Guards"
date: 2018-06-18T13:51:40.750+02:00
subtitle: ""
author: Gabriele Teotino
tags: []
categories: []
draft: true
---

Create a new folder **_guards** in **src/app**.

There is no template for *Guards* so we use *Angular cli*.

```shell
ng g guard /_guards/auth
```

Open **auth.guard.ts** remove the parameters from **canActivate()** because we don't need them.

Add a new constructor and add DI for **AuthService**, **Router** and **AlertifyService**.

Inside the **canActivate** method we check to see if the user is logged in. If not we send a message and redirect to home.

```typescript
...
constructor(private authService: AuthService, private router: Router, private alertify: AlertifyService) {}

canActivate(): Observable<boolean> | Promise<boolean> | boolean {
  if (this.authService.loggedIn()) {
    return true;
  } else {
    this.alertify.error('You need to be logged in to acces this area');
    this.router.navigate(['/home']);
    return false;
  }
}
...
```

Register the *guard* in **app.module.ts** *providers*.

```typescript
providers: [
  AuthService,
  AlertifyService,
  AuthGuard
],
```

Open the *routes* configuration file **routes.ts** and add a dummy route protected by our *guard*.

```typescript
export const ROUTES: Routes = [
  { path: 'home', component: HomeComponent },
  {
    path: '',
    runGuardsAndResolvers: 'always',
    canActivate: [AuthGuard],
    children: [
      { path: 'members', component: MemberListComponent },
      { path: 'lists', component: ListsComponent },
      { path: 'messages', component: MessagesComponent }
    ]
  },
  { path: '**', redirectTo: 'home', pathMatch: 'full' }
];
```

Test the application. Now if the user is logged out and we enter manually the path *http://localhost:4200/messages* then we are redirected to home with an error message.
