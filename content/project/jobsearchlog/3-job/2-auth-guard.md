---
title: "Auth Guard"
date: 2018-09-18T14:45:14.336+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["angular", "typescript", "firebase"]
categories: ["dev"]
draft: true
---

<!--more-->

We need to prevent access to routes for unauthenticated users.

Create a new guard

```shell
ng g guard _guards/auth
```

Edit **auth.guard.ts**, add DI for **AuthService** and **Router**, then check if a user exists.

```typescript
import { Injectable } from '@angular/core';
import {
  CanActivate,
  ActivatedRouteSnapshot,
  RouterStateSnapshot,
  Router
} from '@angular/router';
import { Observable } from 'rxjs';
import { AuthService } from '../_services/auth.service';
import { take, map, tap } from 'rxjs/operators';

@Injectable({
  providedIn: 'root'
})
export class AuthGuard implements CanActivate {
  constructor(private auth: AuthService, private router: Router) {}

  canActivate(
    next: ActivatedRouteSnapshot,
    state: RouterStateSnapshot
  ): Observable<boolean> {
    return this.auth.user.pipe(
      // Complete the observable stream with the first result
      take(1),
      map(user => !!user),
      tap(loggedIn => {
        if (!loggedIn) {
          console.log('access denied');
          this.router.navigate(['/login']);
        }
      })
    );
  }
}
```
