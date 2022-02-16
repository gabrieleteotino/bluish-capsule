---
title: "Auth guard"
date: 2019-02-18T19:38:50+01:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular", "typescript", "ngrx"]
categories: ["dev"]
draft: true
---

Use the Store from an AuthGuard

<!--more-->

Create a new file **auth/auth.guard.ts** with an injectable class **AuthGuard** and register it as a provider in **auth.module.ts**. Then use the guard in the routes of **app.module.ts**

```js
const routes: Routes = [
  {
    path: 'courses',
    loadChildren: './courses/courses.module#CoursesModule',
    canActivate: [AuthGuard]
  },
  {
    path: '**',
    redirectTo: '/'
  }
];
```

The guard code itself is simple and it reuse the store selectors

```js
canActivate(route: ActivatedRouteSnapshot, state: RouterStateSnapshot): Observable<boolean> {
  return this.store.pipe(
    select(isLoggedIn),
    tap(logged => {
      if (!logged) {
        this.router.navigateByUrl('/login');
      }
    })
  );
}
```

Also add a navigate to the **logout** function in **app.component.ts**

```js
logout() {
  this.store.dispatch(new Logout());
  this.router.navigateByUrl('login');
}
```
