---
title: "Route resolvers"
date: 2018-06-29T18:03:31+02:00
subtitle: ""
author: Gabriele Teotino
tags: ["c#", "webapi", "netcore", "angular"]
categories: ["dev"]
draft: true
---

## Member Detail Resolver

Create a new folder **app/_resolvers**. There is no template for a *resolver* using visual studio code and no *generate* template for the cli.

Inside create a **member-detail.resolver.ts** with a class that implements *Resolve<T>* for the **User**.

```typescript
@Injectable()
export class MemberDetailResolver implements Resolve<User> {
  constructor(
    private userService: UserService,
    private router: Router,
    private alertify: AlertifyService
  ) {}

  resolve(route: ActivatedRouteSnapshot): Observable<User> {
    return this.userService.getUser(route.params.id).pipe(
      catchError(error => {
        this.alertify.error('Problem retrieving data');
        this.router.navigate(['/members']);
        return empty();
      })
    );
  }
}
```

Register the *resolver* in the *Providers* in **app.module.ts**.

```typescript
providers: [
  AuthService,
  AlertifyService,
  UserService,
  AuthGuard,
  MemberDetailResolver
],
```

Add the *resolver* in **routes.ts** for the **members/:id** route

```typescript
{
  path: 'members/:id',
  component: MemberDetailComponent,
  resolve: { user: MemberDetailResolver }
},
```

Refactor **member-detail.component.ts** to use the resolver from the route. Also remove the **loadUser** method, the user is now loaded from the route using the resolve.

```typescript
ngOnInit() {
  this.route.data.subscribe(data => {
    this.user = data['user'];
  });
}
```

Another refactor in **member-detail.component.html**: all the safe conditional operators *?.* are no longer needed.

## Member List Resolver

Similarly create a **member-list.resolver.ts** that loads a **User[]**.

```typescript
@Injectable()
export class MemberListResolver implements Resolve<User[]> {
  constructor(
    private userService: UserService,
    private router: Router,
    private alertify: AlertifyService
  ) {}

  resolve(route: ActivatedRouteSnapshot): Observable<User[]> {
    return this.userService.getUsers().pipe(
      catchError(error => {
        this.alertify.error('Problem retrieving data');
        this.router.navigate(['/home']);
        return empty();
      })
    );
  }
}
```

Register as a *Provider* in **app.module.ts**.

Add the *resolver* in **routes.ts** for the **members** route

Refactor **member-list.component.ts**: inject a *route* in the constructor, refactor **ngOnInit** and remove **loadUsers**.

```typescript
export class MemberListComponent implements OnInit {
  users: User[];

  constructor(
    private userService: UserService,
    private alertify: AlertifyService,
    private route: ActivatedRoute
  ) {}

  ngOnInit() {
    this.route.data.subscribe(data => {
      this.users = data['users'];
    });
  }
}
```
