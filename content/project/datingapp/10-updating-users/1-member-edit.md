---
title: "Member edit"
date: 2018-06-29T23:19:43+02:00
subtitle: ""
author: Gabriele Teotino
tags: []
categories: []
draft: true
---

## Component creation

Create a new component **member-edit** in the **members** folder. Add the new component in the *declarations* section of **app.module.ts**.

Open **routes.ts** and add a new route. A *resolver* will be added soon.

```typescript
{ path: 'member/edit', component: MemberEditComponent },
```

Open **nav.component.html** and add a *a-routerLink* in the *Edit profile* anchor.

```html
<li><a [routerLink]="['/member/edit']"><i class="fa fa-user"></i> Edit profile</a></li>
```

Now we can test if the component works.

## Resolver

Create **member-edit.resolver.ts**.

Before we implement the resolver we need to add a new method to **auth.service.ts**

```typescript
getUserId() {
  if (!this.decodedToken) {
    this.loadTokenFromLocalStorage();
  }

  // the token can still be null because we are not logged in
  return this.decodedToken ? this.decodedToken.nameid : '';
}
```

The implementation of the *resolver* is very similar to **member-detail.resolver.ts**

```typescript
@Injectable()
export class MemberEditResolver implements Resolve<User> {
  constructor(
    private userService: UserService,
    private router: Router,
    private alertify: AlertifyService,
    private authService: AuthService
  ) {}

  resolve(route: ActivatedRouteSnapshot): Observable<User> {
    return this.userService.getUser(this.authService.getUserId()).pipe(
      catchError(error => {
        this.alertify.error('Problem retrieving data');
        this.router.navigate(['/home']);
        return empty();
      })
    );
  }
}
```

Add the resolver to the **providers** section of **app.module.ts** and configure the edit route in **routes.ts**.

```typescript
{
  path: 'member/edit',
  component: MemberEditComponent,
  resolve: { user: MemberEditResolver }
},
```

## Implementing the component

Open **member-edit.component.ts** add a **user** property, inject an *ActivatedRoute* an load the user from the resolver.

```typescript
export class MemberEditComponent implements OnInit {
  user: User;

  constructor(private route: ActivatedRoute) {}

  ngOnInit() {
    this.route.data.subscribe(data => {
      this.user = data['user'];
    });
  }
}
```

Open **member-edit.component.html** and add a simple binding.

```html
<p>
  {{user.knownAs}}
</p>
```

Test the application.

## Completing the template
